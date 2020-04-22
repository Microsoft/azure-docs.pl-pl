---
title: Jak wdrożyć usługę zarządzania certyfikatami OPC Vault — Azure | Dokumenty firmy Microsoft
description: Jak wdrożyć usługę zarządzania certyfikatami OPC Vault od podstaw.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686936"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Tworzenie i wdrażanie usługi zarządzania certyfikatami OPC Vault

W tym artykule wyjaśniono, jak wdrożyć usługę zarządzania certyfikatami OPC Vault na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [repozytorium GitHub OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie

Obecnie operacja kompilacji i wdrażania jest ograniczona do systemu Windows.
Przykłady są zapisywane dla języka C# .NET Standard, które należy utworzyć usługę i przykłady do wdrożenia.
Wszystkie narzędzia potrzebne do platformy .NET Standard są wyposażone w narzędzia .NET Core. Zobacz [Wprowadzenie do platformy .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Zainstaluj .NET Core 2.1+][dotnet-install].
2. [Zainstaluj dokceny][docker-url] (opcjonalnie, tylko wtedy, gdy wymagana jest lokalna kompilacja platformy Docker).
4. Zainstaluj [narzędzia wiersza polecenia platformy Azure dla programu PowerShell][powershell-install].
5. Zarejestruj się, aby uzyskać [subskrypcję platformy Azure][azure-free].

### <a name="clone-the-repository"></a>Klonowanie repozytorium

Jeśli jeszcze tego nie zrobiono, sklonuj to repozytorium GitHub. Otwórz wiersz polecenia lub terminal i uruchom następujące czynności:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Alternatywnie można sklonować repozytorium bezpośrednio w programie Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Tworzenie i wdrażanie usługi platformy Azure w systemie Windows

Skrypt programu PowerShell zapewnia łatwy sposób wdrażania mikrousługi OPC Vault i aplikacji.

1. Otwórz okno programu PowerShell w katalogu głównym repozytorium. 
3. Przejdź do folderu `cd deploy`wdrażania .
3. Wybierz nazwę, `myResourceGroup` która prawdopodobnie nie spowoduje konfliktu z innymi wdrożonymi stronami sieci Web. Zobacz sekcję "Nazwa witryny już w użyciu" w dalszej części tego artykułu.
5. Rozpocznij `.\deploy.ps1` wdrożenie do instalacji interaktywnej lub wprowadź pełny wiersz polecenia:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Jeśli planujesz opracowanie za pomocą `-development 1` tego wdrożenia, dodaj, aby włączyć interfejs użytkownika swagger i wdrożyć kompilacje debugowania.
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

8. Otwórz ulubioną przeglądarkę i otwórz stronę aplikacji:`https://myResourceGroup.azurewebsites.net`
8. Daj aplikacji sieci web i mikrousługi OPC Vault kilka minut, aby rozgrzać się po wdrożeniu. Strona główna sieci Web może zawiesić się przy pierwszym użyciu, do minuty, aż do uzyskania pierwszych odpowiedzi.
11. Aby przyjrzeć się interfejsowi API Swagger, otwórz:`https://myResourceGroup-service.azurewebsites.net`
13. Aby uruchomić lokalny serwer GDS z `.\myResourceGroup-gds.cmd`dotnet, uruchom program . Z docker, `.\myResourceGroup-dockergds.cmd`start .

Możliwe jest ponowne wdrożenie kompilacji z dokładnie tymi samymi ustawieniami. Należy pamiętać, że taka operacja odnawia wszystkie wpisy tajne aplikacji i może zresetować niektóre ustawienia w rejestracji aplikacji usługi Azure Active Directory (Azure AD).

Istnieje również możliwość ponownego rozmieszczenia tylko pliki binarne aplikacji sieci web. Z parametrem, `-onlyBuild 1`nowe pakiety zip usługi i aplikacji są wdrażane w aplikacjach internetowych.

Po pomyślnym wdrożeniu można rozpocząć korzystanie z usług. Zobacz [Zarządzanie usługą zarządzania certyfikatami OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Usuwanie usług z subskrypcji

Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do grupy zasobów, w której usługa została wdrożona.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.
4. Po krótkim czasie wszystkie wdrożone składniki usługi są usuwane.
5. Przejdź do rejestracji**aplikacji** **usługi Azure Active Directory** > .
6. Dla każdej wdrożonej grupy zasobów powinny być wymienione trzy rejestracje. Rejestracje mają następujące `resourcegroup-client`nazwy: `resourcegroup-module` `resourcegroup-service`, , . Usuń każdą rejestrację oddzielnie.

Teraz wszystkie wdrożone składniki są usuwane.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrażania

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Użyj krótkiej i prostej nazwy grupy zasobów. Nazwa jest również używana do nazwy zasobów i prefiks adresu URL usługi. W związku z tym musi być zgodna z wymaganiami nazewnictwa zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny już używana

Możliwe, że nazwa strony internetowej jest już używana. Należy użyć innej nazwy grupy zasobów. Nazwy hostów używane przez skrypt wdrażania to:\/https: /resourcegroupname.azurewebsites.net i\/https: /resourgroupname-service.azurewebsites.net.
Inne nazwy usług są budowane przez kombinację skrótów krótkich nazw i jest mało prawdopodobne, aby kolidować z innymi usługami.

### <a name="azure-ad-registration"></a>Rejestracja w usłudze Azure AD 

Skrypt wdrażania próbuje zarejestrować trzy aplikacje usługi Azure AD w usłudze Azure AD. W zależności od uprawnień w wybranej dzierżawy usługi Azure AD ta operacja może zakończyć się niepowodzeniem. Dostępne są dwie opcje:

- Jeśli wybierzesz dzierżawę usługi Azure AD z listy dzierżaw, uruchom ponownie skrypt i wybierz inny z listy.
- Alternatywnie wdrożyć prywatną dzierżawę usługi Azure AD w innej subskrypcji. Uruchom ponownie skrypt i wybierz opcję jego używania.

## <a name="deployment-script-options"></a>Opcje skryptu wdrażania

Skrypt przyjmuje następujące parametry:


```
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```
-subscriptionId
```


Jest to identyfikator subskrypcji, w którym zostaną wdrożone zasoby. Jest to opcjonalne.

```
-subscriptionName
```


Alternatywnie można użyć nazwy subskrypcji.

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

Jest to do wdrożenia w celu rozwoju. Użyj kompilacji debugowania i ustaw środowisko ASP.NET na Programowanie. Utwórz `.publishsettings` do zaimportowania w programie Visual Studio 2017, aby umożliwić mu bezpośrednie wdrożenie aplikacji i usługi. Ten parametr jest również opcjonalny.

```
-onlyBuild 0|1
```

Jest to odbudować i ponownie wdrożyć tylko aplikacje sieci web i odbudować kontenery platformy Docker. Ten parametr jest również opcjonalny.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak wdrożyć OPC Vault od podstaw, możesz:

> [!div class="nextstepaction"]
> [Zarządzanie magazynem OPC](howto-opc-vault-manage.md)
