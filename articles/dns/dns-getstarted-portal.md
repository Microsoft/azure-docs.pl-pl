---
title: 'Szybki start: tworzenie strefy i rekordu DNS — Azure Portal'
titleSuffix: Azure DNS
description: Ten szczegółowy przewodnik Szybki start przedstawia tworzenie strefy usługi Azure DNS i rekordu przy użyciu witryny Azure Portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: d08c5768c2c400ff3d842f58081a58708bfc28db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537783"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Szybki start: tworzenie strefy Azure DNS rekordu przy użyciu Azure Portal

Możesz skonfigurować usługę Azure DNS do rozpoznawania nazw hostów w domenie publicznej. Jeśli na przykład zakupiono nazwę domeny *usługi contoso.xyz* u rejestratora nazw domen, możesz skonfigurować usługę Azure DNS do host contoso.xyz domeny *i* rozpoznać adres IP serwera internetowego lub aplikacji *`www.contoso.xyz`* internetowej.

Podczas pracy z tym przewodnikiem Szybki start utworzysz domenę testową, a następnie rekord adresu powodujący rozpoznawanie nazwy *www* jako adresu IP *10.10.10.10*.

>[!IMPORTANT]
>Wszystkie nazwy i adresy IP w tym przewodniku Szybki start są przykładowe i nie reprezentują rzeczywistych scenariuszy.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W przypadku wszystkich kroków wykonywanych w portalu należy się zalogować do [witryny Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny. 

**Aby utworzyć strefę DNS:**

1. W lewym górnym rogu wybierz pozycję **Utwórz zasób**, a następnie **Sieć** i **Strefa DNS**.

1. Na stronie **Tworzenie strefy DNS** wpisz lub wybierz następujące wartości:

   - **Nazwa**: wpisz *contoso.xyz* dla przykładu w tym przewodniku Szybki start. Nazwa strefy DNS może być dowolną wartością, która nie jest już skonfigurowana na serwerach usługi Azure DNS. W rzeczywistym scenariuszu tą wartością byłaby domena kupiona u rejestratora nazw domen.
   - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź *nazwę MyResourceGroup* i wybierz przycisk **OK.** Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure. 

1. Wybierz przycisk **Utwórz**.

   ![Strefa DNS](./media/dns-getstarted-portal/openzone650.png)

Tworzenie strefy może potrwać kilka minut.

## <a name="create-a-dns-record&quot;></a>Tworzenie rekordu DNS

Wpisy, czyli rekordy, DNS tworzy się dla domeny w strefie DNS. Utwórz nowy rekordu adresu, czyli rekord „A”, aby umożliwić rozpoznawanie nazwy hosta jako adresu IPv4.

**Aby utworzyć rekord „A”:**

1. W witrynie Azure Portal obszarze **Wszystkie** zasoby otwórz **contoso.xyz** DNS w grupie zasobów **MyResourceGroup.** Wartość *contoso.xyz* można wpisać w polu **Filtruj według nazwy**, aby łatwiej znaleźć strefę.

1. W górnej części strony **Strefa DNS** wybierz pozycję **+ Zestaw rekordów**.

1. Na stronie **Dodawanie zestawu rekordów** wpisz lub wybierz następujące wartości:

   - **Nazwa**: wpisz wartość *www*. Nazwa rekordu to nazwa hosta, która ma być rozpoznawana jako określony adres IP.
   - **Typ:** wybierz **typ**. Rekordy &quot;A&quot; są najczęściej spotykane, ale istnieją inne typy rekordów dla serwerów poczty e-mail (&quot;MX"), adresów IP w wersji 6 ("AAAA"), i tak dalej. 
   - **Czas wygaśnięcia**: wpisz wartość *1*. *Czas wygaśnięcia* żądania DNS określa, jak długo serwery i klienci DNS mogą buforować odpowiedź.
   - **Jednostka czasu wygaśnięcia**: wybierz opcję **Godziny**. Jest to jednostka czasu dla wartości **Czas wygaśnięcia**. 
   - **Adres IP**: dla tego przykładu przewodnika Szybki start wpisz *10.10.10.10*. Ta wartość to adres IP rozpoznawany w przypadku nazwy rekordu. W rzeczywistym scenariuszu należy wprowadzić publiczny adres IP swojego serwera internetowego.

Ponieważ ten przewodnik Szybki start jest tylko do celów szybkiego testowania, nie ma potrzeby konfigurowania serwerów nazw Azure DNS u rejestratora nazw domen. W przypadku rzeczywistej domeny produkcyjnej chcesz, aby każda osoba w Internecie rozpoznała nazwę hosta w celu nawiązania połączenia z serwerem internetowym lub aplikacją. Odwiedzisz rejestratora nazw domen, aby zastąpić rekordy serwera nazw serwerami nazw usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testowanie rozpoznawania nazw

Po utworzeniu testowej strefy DNS z rekordem „A” możesz przetestować rozpoznawanie nazw za pomocą narzędzia o nazwie *nslookup*. 

**Aby przetestować rozpoznawanie nazw DNS:**

1. W witrynie Azure Portal obszarze **Wszystkie** zasoby otwórz **contoso.xyz** DNS w grupie zasobów **MyResourceGroup.** Wartość *contoso.xyz* można wpisać w polu **Filtruj według nazwy**, aby łatwiej znaleźć strefę.

1. Skopiuj jedną z nazw serwerów nazw z listy serwerów nazw na stronie **Przegląd**. 

   ![strefa](./media/dns-getstarted-portal/viewzonens500.png)

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Na przykład:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Powinna zostać wyświetlona treść podobna do tej na następującym ekranie:

   ![Zrzut ekranu przedstawia okno wiersza polecenia z n s wyszukiwania polecenia i wartości dla serwera, adres, nazwa, i adres.](media/dns-getstarted-portal/nslookup.PNG)

Nazwa hosta **www \. contoso.xyz** jest rozpoznawczy **do 10.10.10.10**, tak jak skonfigurowano. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w tym przewodniku Szybki start, usuń je, usuwając grupę zasobów **MyResourceGroup.** Otwórz **grupę zasobów MyResourceGroup** i wybierz pozycję **Usuń grupę zasobów.**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
