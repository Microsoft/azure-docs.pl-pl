---
title: Przekierowywanie z protokołu HTTP do HTTPS w portalu — Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji z przekierowywanym ruchem z protokołu HTTP do HTTPS przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e7fb40d95c4659bf353366770da7c903ffa1bd09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867227"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Tworzenie bramy aplikacji z przekierowywaniem protokołu HTTP do HTTPS przy użyciu Azure Portal

Możesz użyć tej Azure Portal, aby utworzyć bramę [aplikacji z](overview.md) certyfikatem na użytek zakończenia TLS. Reguła routingu służy do przekierowywania ruchu HTTP do portu HTTPS w bramie aplikacji. W tym przykładzie utworzysz również zestaw [skalowania](../virtual-machine-scale-sets/overview.md) maszyn wirtualnych dla puli zaplecza bramy aplikacji, która zawiera dwa wystąpienia maszyn wirtualnych.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie certyfikatu z podpisem własnym
* Konfigurowanie sieci
* Tworzenie bramy aplikacji z certyfikatem
* Dodawanie odbiornika i reguły przekierowania
* Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga Azure PowerShell w wersji 1.0.0 lub nowszej, aby utworzyć certyfikat i zainstalować usługi IIS. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchomić polecenia w tym samouczku, należy również uruchomić polecenie , aby `Login-AzAccount` utworzyć połączenie z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użytku produkcyjnego należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Wynik powinien wyglądać podobnie do następującego:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
4. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/create-url-route-portal/application-gateway-create.png)

5. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
6. Kliknij kolejno pozycje **Wybierz sieć wirtualną**, **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknij przycisk **OK**, aby utworzyć sieć wirtualną i podsieć.
8. W **obszarze Konfiguracja adresu IP frontony** upewnij się, że typ adresu **IP** to **Publiczny** i **wybrano** opcję Utwórz nowy. Wprowadź *myAGPublicIPAddress* jako nazwę. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
9. W **obszarze Konfiguracja odbiornika** wybierz **pozycję HTTPS,** a następnie wybierz pozycję Wybierz **plik** i przejdź do *pliku c:\appgwcert.pfx* i wybierz pozycję **Otwórz.**
10. Wpisz *appgwcert* jako nazwę certyfikatu i *azure123456!* jako hasło.
11. Pozostaw wyłączono zaporę aplikacji internetowej, a następnie wybierz przycisk **OK.**
12. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz przycisk **OK,** aby utworzyć zasoby sieciowe i bramę aplikacji. Przed przejściem do następnej sekcji może potrwać kilka minut, zanim brama aplikacji zostanie utworzona. Zaczekaj, aż wdrożenie zakończy się pomyślnie.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz **pozycję Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myVNet** z listy zasobów.
2. Wybierz **pozycję Podsieci,** a następnie kliknij pozycję **Podsieć.**

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Wpisz *myBackendSubnet* jako nazwę podsieci.
4. Wpisz *10.0.2.0/24* jako zakres adresów, a następnie wybierz przycisk **OK.**

## <a name="add-a-listener-and-redirection-rule"></a>Dodawanie odbiornika i reguły przekierowania

### <a name="add-the-listener"></a>Dodawanie odbiornika

Najpierw dodaj odbiornik o nazwie *myListener* dla portu 80.

1. Otwórz **grupę zasobów myResourceGroupAG** i wybierz **pozycję myAppGateway.**
2. Wybierz **pozycję Odbiorniki,** a następnie wybierz **pozycję + Podstawowa.**
3. Wpisz *MyListener* jako nazwę.
4. Wpisz *httpPort* jako nazwę nowego portu frontonia i *80* jako port.
5. Upewnij się, że dla protokołu ustawiono **wartość HTTP,** a następnie wybierz przycisk **OK.**

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Dodawanie reguły rozsyłania z konfiguracją przekierowania

1. W **bramie myAppGateway** wybierz pozycję **Reguły,** a następnie wybierz **pozycję +Reguła rozsyłania żądania**.
2. W nazwie **reguły wpisz** *Rule2*.
3. Upewnij się, że dla odbiornika wybrano opcję **MyListener.**
4. Kliknij **kartę Cele zaplecza** i wybierz **pozycję Typ docelowy** jako *Przekierowywanie.*
5. W **przypadku opcji Redirection type (Typ** przekierowania) **wybierz opcję Permanent (Trwałe).**
6. W **przypadku docelowego przekierowania** wybierz **pozycję Odbiornik**.
7. Upewnij **się, że odbiornik docelowy** został ustawiony na **wartość appGatewayHttpListener.**
8. W przypadku **opcji Uwzględnij ciąg zapytania** i Ścieżka **dołączania wybierz** pozycję *Tak.*
9. Wybierz pozycję **Dodaj**.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji.

1. W lewym górnym rogu portalu wybierz pozycję **+Utwórz zasób.**
2. Wybierz pozycję **Compute**.
3. W polu wyszukiwania wpisz zestaw *skalowania* i naciśnij klawisz Enter.
4. Wybierz **pozycję Zestaw skalowania maszyn wirtualnych,** a następnie wybierz **pozycję Utwórz.**
5. W **przypadku nazwy zestawu skalowania maszyn wirtualnych** wpisz *myvmss*.
6. W przypadku obrazu dysku systemu operacyjnego** upewnij się, że wybrano opcję **Windows Server 2016 Datacenter.**
7. W **przypadku grupy zasobów** wybierz pozycję **myResourceGroupAG.**
8. W **przypadku nazwy użytkownika** wpisz *azureuser*.
9. W **przypadku hasła** wpisz *Azure123456!* i potwierdź hasło.
10. W **przypadku liczby wystąpień** upewnij się, że wartość wynosi **2**.
11. W **przypadku opcji Rozmiar** wystąpienia wybierz pozycję **D2s_v3**.
12. W **obszarze Sieć** upewnij się, że opcja Wybierz opcje **równoważenia obciążenia** jest ustawiona na **Application Gateway**.
13. Upewnij **się, że dla** bramy aplikacji **ustawiono wartość myAppGateway.**
14. Upewnij **się, że dla** podsieci ustawiono wartość **myBackendSubnet.**
15. Wybierz przycisk **Utwórz**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Kojarzenie zestawu skalowania z odpowiednią pulą zaplecza

Interfejs użytkownika portalu zestawu skalowania maszyn wirtualnych tworzy nową pulę zaplecza dla zestawu skalowania, ale chcesz skojarzyć ją z istniejącą pulą appGatewayBackendPool.

1. Otwórz **grupę zasobów myResourceGroupAg.**
2. Wybierz **pozycję myAppGateway.**
3. Wybierz **pozycję Pule zaplecza.**
4. Wybierz **pozycję myAppGatewaymyvmss.**
5. Wybierz **pozycję Usuń wszystkie obiekty docelowe z puli zaplecza.**
6. Wybierz pozycję **Zapisz**.
7. Po zakończeniu tego procesu wybierz pulę zaplecza **myAppGatewaymyvmss,** wybierz pozycję **Usuń,** a następnie kliknij **przycisk OK,** aby potwierdzić.
8. Wybierz pozycję **appGatewayBackendPool**.
9. W **obszarze Targets (Cele)** **wybierz pozycję VMSS**.
10. W **obszarze VMSS** wybierz **pozycję myvmss.**
11. W **obszarze Konfiguracje interfejsu sieciowego** **wybierz pozycję myvmssNic.**
12. Wybierz pozycję **Zapisz**.

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Na koniec należy uaktualnić zestaw skalowania przy użyciu tych zmian.

1. Wybierz zestaw **skalowania myvmss.**
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Wybierz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij.**
4. Wybierz pozycję **Tak**, aby potwierdzić.
5. Po zakończeniu tego procesu wróć do bramy **myAppGateway** i wybierz **pozycję Pule zaplecza.** Teraz powinno być widać, że **puli appGatewayBackendPool** mają dwa obiekty docelowe, a  **myAppGatewaymyvmss ma** zero obiektów docelowych.
6. Wybierz **pozycję myAppGatewaymyvmss,** a następnie wybierz pozycję **Usuń.**
7. Wybierz przycisk **OK**, aby potwierdzić.

### <a name="install-iis"></a>Instalowanie usług IIS

Łatwym sposobem zainstalowania usług IIS w zestawie skalowania jest użycie programu PowerShell. W portalu kliknij ikonę Cloud Shell i upewnij się, że wybrano **program PowerShell.**

Wklej następujący kod w oknie programu PowerShell i naciśnij klawisz Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Po zmianie wystąpień za pomocą usług IIS należy ponownie uaktualnić zestaw skalowania przy użyciu tej zmiany.

1. Wybierz zestaw **skalowania myvmss.**
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Wybierz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij.**
4. Wybierz pozycję **Tak**, aby potwierdzić.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Publiczny adres IP aplikacji można uzyskać ze strony Przegląd bramy aplikacji.

1. Wybierz **pozycję myAppGateway.**
2. Na stronie **Przegląd** zanotuj adres IP w obszarze **Publiczny adres IP frontonia**.

3. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://52.170.203.149

   ![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

   ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak utworzyć bramę aplikacji z wewnętrznym przekierowaniem.](redirect-internal-site-powershell.md)
