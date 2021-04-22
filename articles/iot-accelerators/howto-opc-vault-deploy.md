---
title: Jak wdrożyć usługę zarządzania certyfikatami magazynu OPC — Azure | Microsoft Docs
description: Jak od podstaw wdrożyć usługę zarządzania certyfikatami magazynu OPC.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870287"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Kompilowanie i wdrażanie usługi zarządzania certyfikatami magazynu OPC

> [!IMPORTANT]
> Podczas aktualizowania tego artykułu zobacz [Artykuł Azure Industrial IoT (Azure Industrial IoT)](https://azure.github.io/Industrial-IoT/) zawiera najbardziej aktualne informacje.

W tym artykule wyjaśniono, jak wdrożyć usługę zarządzania certyfikatami magazynu OPC na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz repozytorium usługi GitHub [OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-required-software"></a>Instalowanie wymaganego oprogramowania

Obecnie operacja kompilacji i wdrażania jest ograniczona do systemu Windows.
Wszystkie przykłady są napisane dla języka C# .NET Standard, które są potrzebne do skompilowania usługi i przykładów na potrzeby wdrożenia.
Wszystkie narzędzia potrzebne do .NET Standard są dostępne z narzędziami .NET Core. Zobacz [Rozpoczynanie pracy z programem .NET Core.](/dotnet/articles/core/getting-started)

1. [Zainstaluj program .NET Core 2.1+][dotnet-install].
2. [Zainstaluj aplikację Docker][docker-url] (opcjonalnie tylko wtedy, gdy jest wymagana lokalna kompilacja platformy Docker).
4. Zainstaluj narzędzia [wiersza polecenia platformy Azure dla programu PowerShell.][powershell-install]
5. Zarejestruj się, aby uzyskać [subskrypcję platformy Azure.][azure-free]

### <a name="clone-the-repository"></a>Klonowanie repozytorium

Jeśli jeszcze tego nie zrobiono, sklonuj to repozytorium GitHub. Otwórz wiersz polecenia lub terminal i uruchom następujące polecenie:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Możesz również sklonować to repo bezpośrednio w programie Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Kompilowanie i wdrażanie usługi platformy Azure w systemie Windows

Skrypt programu PowerShell umożliwia łatwe wdrożenie mikrousługi i aplikacji magazynu OPC.

1. Otwórz okno programu PowerShell w katalogu głównym repo. 
3. Przejdź do folderu deploy `cd deploy` .
3. Wybierz nazwę, która prawdopodobnie nie spowoduje konfliktu `myResourceGroup` z innymi wdrożonymi stronami internetowymi. Zobacz sekcję "Nazwa witryny internetowej jest już w użyciu" w dalszej części tego artykułu.
5. Rozpocznij wdrażanie za pomocą `.\deploy.ps1` polecenia w celu instalacji interakcyjnej lub wprowadź pełny wiersz polecenia:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Jeśli planujesz programować przy użyciu tego wdrożenia, dodaj polecenie , aby włączyć interfejs użytkownika `-development 1` programu Swagger i wdrożyć kompilacje debugowania.
6. Postępuj zgodnie z instrukcjami w skrypcie, aby zalogować się do subskrypcji i podać dodatkowe informacje.
9. Po pomyślnej operacji kompilacji i wdrażania powinien zostać wyświetlony następujący komunikat:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > W przypadku problemów zobacz sekcję "Rozwiązywanie problemów z błędami wdrażania" w dalszej części artykułu.

8. Otwórz ulubioną przeglądarkę i otwórz stronę aplikacji: `https://myResourceGroup.azurewebsites.net`
8. Nadaj aplikacji internetowej i mikrousługi magazynu OPC kilka minut rozgrzewki po wdrożeniu. Strona główna sieci Web może przestać odpowiadać przy pierwszym użyciu przez maksymalnie minutę, dopóki nie otrzymasz pierwszych odpowiedzi.
11. Aby przyjrzeć się interfejsowi API swagger, otwórz: `https://myResourceGroup-service.azurewebsites.net`
13. Aby uruchomić lokalny serwer GDS za pomocą programu dotnet, `.\myResourceGroup-gds.cmd` uruchom . Za pomocą platformy Docker `.\myResourceGroup-dockergds.cmd` uruchom .

Można ponownie wdedytować kompilację przy użyciu dokładnie tych samych ustawień. Należy pamiętać, że taka operacja odnawia wszystkie wpisy tajne aplikacji i może zresetować niektóre ustawienia w Azure Active Directory rejestracji aplikacji (Azure AD).

Możliwe jest również ponowne wdanie tylko plików binarnych aplikacji internetowej. Za pomocą parametru nowe pakiety zip usługi i `-onlyBuild 1` aplikacji są wdrażane w aplikacjach internetowych.

Po pomyślnym wdrożeniu możesz rozpocząć korzystanie z usług. Zobacz [Zarządzanie usługą zarządzania certyfikatami magazynu OPC.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Usuwanie usług z subskrypcji

Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do grupy zasobów, w której wdrożono usługę.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.
4. Po krótkiej chwili wszystkie wdrożone składniki usługi zostaną usunięte.
5. Przejdź do **Azure Active Directory**  >  **Rejestracje aplikacji**.
6. Dla każdej wdrożonej grupy zasobów powinny być wymienione trzy rejestracje. Rejestracje mają następujące nazwy: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Usuń każdą rejestrację oddzielnie.

Teraz wszystkie wdrożone składniki są usuwane.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrażania

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Użyj krótkiej i prostej nazwy grupy zasobów. Nazwa jest również używana do nazywania zasobów i prefiksu adresu URL usługi. W związku z tym musi spełniać wymagania dotyczące nazewnictwa zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny internetowej jest już w użyciu

Istnieje możliwość, że nazwa witryny internetowej jest już w użyciu. Musisz użyć innej nazwy grupy zasobów. Nazwy hostów w użyciu przez skrypt wdrażania to: https: \/ /resourcegroupname.azurewebsites.net i https: \/ /resourgroupname-service.azurewebsites.net.
Inne nazwy usług są budowane przez kombinację skrótów krótkich nazw i prawdopodobnie nie będą kolidować z innymi usługami.

### <a name="azure-ad-registration"></a>Rejestracja w usłudze Azure AD 

Skrypt wdrażania próbuje zarejestrować trzy aplikacje usługi Azure AD w usłudze Azure AD. W zależności od uprawnień w wybranej dzierżawie usługi Azure AD ta operacja może się nie powieść. Dostępne są dwie opcje:

- Jeśli wybrano dzierżawę usługi Azure AD z listy dzierżaw, uruchom ponownie skrypt i wybierz inny z listy.
- Możesz też wdrożyć prywatną dzierżawę usługi Azure AD w innej subskrypcji. Uruchom ponownie skrypt i wybierz jego użycie.

## <a name="deployment-script-options"></a>Opcje skryptu wdrażania

Skrypt przyjmuje następujące parametry:


```
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```
-subscriptionId
```


Jest to identyfikator subskrypcji, w której zostaną wdrożone zasoby. Jest to opcjonalne.

```
-subscriptionName
```


Alternatywnie możesz użyć nazwy subskrypcji.

```
-resourceGroupLocation
```


Jest to lokalizacja grupy zasobów. Jeśli zostanie określony, ten parametr próbuje utworzyć nową grupę zasobów w tej lokalizacji. Ten parametr jest również opcjonalny.


```
-tenantId
```


Jest to dzierżawa usługi Azure AD do użycia. 

```
-development 0|1
```

Jest to wdrożenie na platformie dewelopera. Użyj kompilacji debugowania i ustaw środowisko ASP.NET na development. Utwórz `.publishsettings` do zaimportowania Visual Studio 2017 r., aby umożliwić jej bezpośrednie wdrożenie aplikacji i usługi. Ten parametr jest również opcjonalny.

```
-onlyBuild 0|1
```

Ma to na celu ponowne skompilowanie i ponowne wdanie tylko aplikacji internetowych oraz ponowne skompilowanie kontenerów platformy Docker. Ten parametr jest również opcjonalny.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć magazyn OPC od podstaw, możesz:

> [!div class="nextstepaction"]
> [Zarządzanie magazynem OPC](howto-opc-vault-manage.md)