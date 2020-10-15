---
title: Jak wdrożyć usługę zarządzania certyfikatami magazynu OPC — Azure | Microsoft Docs
description: Jak wdrożyć usługę zarządzania certyfikatami magazynu OPC od podstaw.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 98dc9345d2c8b392fd094458b612857d6d454739
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071493"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Kompilowanie i wdrażanie usługi zarządzania certyfikatami magazynu OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

W tym artykule opisano sposób wdrażania usługi zarządzania certyfikatami magazynu OPC na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [repozytorium magazynu OPC](https://github.com/Azure/azure-iiot-opc-vault-service)usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie

Obecnie operacja kompilowania i wdrażania jest ograniczona do systemu Windows.
Przykłady są przeznaczone dla .NET Standard w języku C#, co jest potrzebne do utworzenia usługi i przykładów do wdrożenia.
Wszystkie narzędzia potrzebne do .NET Standard są dostarczane z narzędziami programu .NET Core. Zobacz Rozpoczynanie [pracy z platformą .NET Core](/dotnet/articles/core/getting-started).

1. [Zainstaluj program .NET Core 2.1 +][dotnet-install].
2. [Zainstaluj platformę Docker][docker-url] (opcjonalnie tylko wtedy, gdy wymagana jest lokalna kompilacja platformy Docker).
4. Zainstaluj [narzędzia wiersza polecenia platformy Azure dla programu PowerShell][powershell-install].
5. Zarejestruj się, aby uzyskać [subskrypcję platformy Azure][azure-free].

### <a name="clone-the-repository"></a>Klonowanie repozytorium

Jeśli jeszcze tego nie zrobiono, Sklonuj to repozytorium GitHub. Otwórz wiersz polecenia lub terminal i uruchom następujące polecenie:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Alternatywnie można sklonować repozytorium bezpośrednio w programie Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Kompilowanie i wdrażanie usługi platformy Azure w systemie Windows

Skrypt programu PowerShell zapewnia łatwy sposób wdrażania mikrousług magazynu OPC i aplikacji.

1. Otwórz okno programu PowerShell w katalogu głównym repozytorium. 
3. Przejdź do folderu Wdróż `cd deploy` .
3. Wybierz nazwę `myResourceGroup` , która prawdopodobnie nie spowoduje konfliktu z innymi wdrożonymi stronami sieci Web. Zobacz sekcję "Nazwa witryny sieci Web, która jest już używana" w dalszej części tego artykułu.
5. Rozpocznij wdrażanie za pomocą `.\deploy.ps1` programu dla instalacji interaktywnej lub wprowadź pełny wiersz polecenia:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Jeśli planujesz Programowanie przy użyciu tego wdrożenia, Dodaj, `-development 1` Aby włączyć interfejs użytkownika programu Swagger, i wdrożyć kompilacje debugowania.
6. Postępuj zgodnie z instrukcjami w skrypcie, aby zalogować się do subskrypcji i podać dodatkowe informacje.
9. Po pomyślnym zakończeniu kompilacji i wdrożenia powinien zostać wyświetlony następujący komunikat:
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
   > W razie problemów zapoznaj się z sekcją "Rozwiązywanie problemów z błędami wdrażania" w dalszej części artykułu.

8. Otwórz ulubioną przeglądarkę i Otwórz stronę aplikacji: `https://myResourceGroup.azurewebsites.net`
8. Nadajesz aplikacji sieci Web i mikrousługom magazynu OPC kilka minut, aby rozgrzać po wdrożeniu. Strona główna sieci Web może przestać odpowiadać przy pierwszym użyciu, przez maksymalnie minutę, aż do momentu otrzymania pierwszych odpowiedzi.
11. Aby zapoznać się z interfejsem API struktury Swagger, Otwórz: `https://myResourceGroup-service.azurewebsites.net`
13. Aby uruchomić lokalny serwer GDS z programem dotnet, uruchom polecenie `.\myResourceGroup-gds.cmd` . Przy użyciu platformy Docker Uruchom polecenie `.\myResourceGroup-dockergds.cmd` .

Można ponownie wdrożyć kompilację z dokładnie tymi samymi ustawieniami. Należy pamiętać, że taka operacja odnawia wszystkie wpisy tajne aplikacji i może zresetować niektóre ustawienia w rejestracjach aplikacji Azure Active Directory (Azure AD).

Możliwe jest również ponowne wdrożenie tylko plików binarnych aplikacji sieci Web. Przy użyciu parametru `-onlyBuild 1` nowe pakiety zip usługi i aplikacji są wdrażane w aplikacjach sieci Web.

Po pomyślnym wdrożeniu można rozpocząć korzystanie z usług. Zobacz [Zarządzanie usługą zarządzania certyfikatami magazynu OPC](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Usuwanie usług z subskrypcji

Oto kroki tej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do grupy zasobów, w której została wdrożona usługa.
3. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.
4. Po krótkim czasie wszystkie wdrożone składniki usługi zostaną usunięte.
5. Przejdź do **Azure Active Directory**  >  **rejestracje aplikacji**Azure Active Directory.
6. Dla każdej wdrożonej grupy zasobów powinny być wymienione trzy rejestracje. Rejestracje mają następujące nazwy: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Usuń każdą rejestrację osobno.

Teraz wszystkie wdrożone składniki są usuwane.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Użyj krótkiej i prostej nazwy grupy zasobów. Nazwa jest również używana do nazwy zasobów i prefiksu adresu URL usługi. W związku z tym musi być zgodna z wymaganiami dotyczącymi nazewnictwa zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny sieci Web jest już używana

Istnieje możliwość, że nazwa witryny sieci Web jest już używana. Należy użyć innej nazwy grupy zasobów. Nazwy hostów używane przez skrypt wdrażania są następujące: https: \/ /ResourceGroupName.azurewebsites.NET i https: \/ /resourgroupname-Service.azurewebsites.NET.
Inne nazwy usług są tworzone przez kombinację skrótów krótkich nazw i prawdopodobnie nie powodują konfliktu z innymi usługami.

### <a name="azure-ad-registration"></a>Rejestracja w usłudze Azure AD 

Skrypt wdrażania próbuje zarejestrować trzy aplikacje usługi Azure AD w usłudze Azure AD. W zależności od uprawnień w wybranej dzierżawie usługi Azure AD ta operacja może zakończyć się niepowodzeniem. Dostępne są dwie opcje:

- W przypadku wybrania dzierżawy usługi Azure AD z listy dzierżawców Uruchom ponownie skrypt i wybierz inny z listy.
- Alternatywnie Wdróż prywatną dzierżawę usługi Azure AD w innej subskrypcji. Uruchom ponownie skrypt i wybierz, aby go użyć.

## <a name="deployment-script-options"></a>Opcje skryptu wdrożenia

Skrypt przyjmuje następujące parametry:


```
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```
-subscriptionId
```


To jest Identyfikator subskrypcji, w której zostaną wdrożone zasoby. Jest to opcjonalne.

```
-subscriptionName
```


Alternatywnie możesz użyć nazwy subskrypcji.

```
-resourceGroupLocation
```


Jest to lokalizacja grupy zasobów. Jeśli ta wartość jest określona, ten parametr próbuje utworzyć nową grupę zasobów w tej lokalizacji. Ten parametr jest również opcjonalny.


```
-tenantId
```


To jest dzierżawa usługi Azure AD do użycia. 

```
-development 0|1
```

Jest to wdrożenie na potrzeby programowania. Użyj opcji Kompilacja debugowania i ustaw środowisko ASP.NET na programowanie. Utwórz `.publishsettings` element do zaimportowania w programie Visual Studio 2017, aby umożliwić jego bezpośrednie wdrożenie aplikacji i usługi. Ten parametr jest również opcjonalny.

```
-onlyBuild 0|1
```

Polega to na ponownym odbudowaniu i ponownym wdrożeniu tylko aplikacji sieci Web oraz w celu odbudowania kontenerów platformy Docker. Ten parametr jest również opcjonalny.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć magazyn OPC od podstaw, możesz:

> [!div class="nextstepaction"]
> [Zarządzanie magazynem OPC](howto-opc-vault-manage.md)