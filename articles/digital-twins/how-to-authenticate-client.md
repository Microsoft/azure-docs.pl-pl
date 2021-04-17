---
title: Pisanie kodu uwierzytelniania aplikacji
titleSuffix: Azure Digital Twins
description: Zobacz, jak napisać kod uwierzytelniania w aplikacji klienckiej
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589399"
---
# <a name="write-client-app-authentication-code"></a>Pisanie kodu uwierzytelniania aplikacji klienckiej

Po [skonfigurowaniu wystąpienia Azure Digital Twins uwierzytelniania](how-to-set-up-instance-portal.md)można utworzyć aplikację kliency, która będzie używać do interakcji z wystąpieniem. Po skonfigurowaniu projektu klienta początkowego należy napisać  w tej aplikacji klienckiej kod w celu uwierzytelnienia go w Azure Digital Twins wystąpienia.

Azure Digital Twins przeprowadza uwierzytelnianie przy [użyciu tokenów zabezpieczających usługi Azure AD na podstawie protokołu OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Aby uwierzytelnić zestaw SDK, musisz uzyskać token okaziciela z uprawnieniami do uwierzytelniania Azure Digital Twins i przekazać go wraz z wywołaniami interfejsu API. 

W tym artykule opisano sposób uzyskiwania poświadczeń przy użyciu `Azure.Identity` biblioteki klienta. Chociaż w tym artykule przedstawiono przykłady kodu w języku C#, takie jak to, co należy napisać dla zestawu SDK platformy [.NET (C#),](/dotnet/api/overview/azure/digitaltwins/client)możesz użyć wersji niezależnie od tego, jakiego zestawu SDK używasz (aby uzyskać więcej informacji na temat zestawów SDK dostępnych dla platformy Azure Digital Twins, zobacz `Azure.Identity` [*How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md)(Jak używać interfejsów API i zestawów SDK platformy Azure Digital Twins).

## <a name="prerequisites"></a>Wymagania wstępne

Najpierw wykonaj kroki konfiguracji opisane w te [*tematu Instrukcje: Konfigurowanie wystąpienia i uwierzytelniania.*](how-to-set-up-instance-portal.md) Dzięki temu masz wystąpienie Azure Digital Twins i że użytkownik ma uprawnienia dostępu. Po tej konfiguracji możesz napisać kod aplikacji klienckiej.

Aby kontynuować, będziesz potrzebować projektu aplikacji klienckiej, w którym piszesz kod. Jeśli nie masz jeszcze projektu aplikacji klienckiej, utwórz podstawowy projekt w języku, który będzie używany w tym samouczku.

## <a name="common-authentication-methods-with-azureidentity"></a>Typowe metody uwierzytelniania za pomocą usługi Azure.Identity

`Azure.Identity` to biblioteka klienta, która udostępnia kilka metod uzyskiwania poświadczeń, których można użyć do uzyskania tokenu okaziciela i uwierzytelnienia za pomocą zestawu SDK. Chociaż ten artykuł zawiera przykłady w języku C#, można je `Azure.Identity` wyświetlić w kilku językach, w tym...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Trzy typowe metody uzyskiwania poświadczeń w programie `Azure.Identity` to:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) zapewnia domyślny przepływ uwierzytelniania dla aplikacji, które zostaną wdrożone na platformie Azure, i jest zalecanym wyborem `TokenCredential` w przypadku tworzenia aplikacji **lokalnych.** Można ją również włączyć, aby wypróbować dwie inne metody zalecane w tym artykule. `ManagedIdentityCredential` Opakowywuje i może uzyskać dostęp za pomocą zmiennej `InteractiveBrowserCredential` konfiguracji.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) działa świetnie w przypadkach, gdy potrzebujesz tożsamości zarządzanych [(MSI),](../active-directory/managed-identities-azure-resources/overview.md)i jest dobrym kandydatem do pracy z usługami Azure Functions wdrażania w usługach platformy Azure.
* [Element InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) jest przeznaczony dla aplikacji interaktywnych i może służyć do tworzenia uwierzytelnionego klienta zestawu SDK

W poniższym przykładzie pokazano, jak używać każdego z nich z zestawem SDK platformy .NET (C#).

## <a name="authentication-examples-net-c-sdk"></a>Przykłady uwierzytelniania: zestaw SDK platformy .NET (C#)

W tej sekcji przedstawiono przykład w języku C# użycia dostarczonego zestawu SDK platformy .NET do napisania kodu uwierzytelniania.

Najpierw uwzględnij pakiet SDK `Azure.DigitalTwins.Core` i `Azure.Identity` pakiet w projekcie. W zależności od wybranego narzędzia można dołączyć pakiety przy użyciu Visual Studio pakietu lub `dotnet` narzędzia wiersza polecenia. 

Do kodu projektu należy również dodać następujące instrukcje using:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Następnie dodaj kod, aby uzyskać poświadczenia przy użyciu jednej z metod w `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential, metoda

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) zapewnia domyślny przepływ uwierzytelniania dla aplikacji, które zostaną wdrożone na platformie Azure, i jest zalecanym `TokenCredential` wyborem w przypadku tworzenia aplikacji **lokalnych.**

Aby użyć domyślnych poświadczeń platformy Azure, musisz mieć adres URL Azure Digital Twins wystąpienia[(instrukcje dotyczące znalezienia ).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Oto przykładowy kod do dodania do `DefaultAzureCredential` projektu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Konfigurowanie lokalnych poświadczeń platformy Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential, metoda

Metoda [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) działa świetnie w przypadkach, gdy potrzebujesz tożsamości zarządzanych [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)— na przykład podczas pracy z Azure Functions.

Oznacza to, że możesz użyć w tym samym projekcie co lub , aby `ManagedIdentityCredential` `DefaultAzureCredential` `InteractiveBrowserCredential` uwierzytelnić inną część projektu.

Aby użyć domyślnych poświadczeń platformy Azure, potrzebny będzie adres URL Azure Digital Twins wystąpienia[(instrukcje dotyczące znalezienia polecenia](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

W funkcji platformy Azure możesz użyć poświadczeń tożsamości zarządzanej w taki sposób:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential, metoda

Metoda [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) jest przeznaczona dla aplikacji interaktywnych i umożliwia uwierzytelnianie za pomocą przeglądarki internetowej. Można jej użyć zamiast w `DefaultAzureCredential` przypadkach, w których wymagane jest uwierzytelnianie interakcyjne.

Aby korzystać z poświadczeń przeglądarki  interakcyjnej, musisz mieć rejestrację aplikacji z uprawnieniami do interfejsów API Azure Digital Twins. Aby uzyskać instrukcje dotyczące sposobu skonfigurowania tej rejestracji aplikacji, zobacz [*Instrukcje: Tworzenie rejestracji aplikacji.*](how-to-create-app-registration.md) Po skonfigurowaniu rejestracji aplikacji będziesz potrzebować...
* identyfikator aplikacji *(klienta)* rejestracji aplikacji [(instrukcje dotyczące znalezienia](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* identyfikator katalogu *(dzierżawy)* rejestracji aplikacji [(instrukcje dotyczące znalezienia](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* adres URL Azure Digital Twins wystąpienia[(instrukcje dotyczące znalezienia](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Oto przykład kodu służącego do tworzenia uwierzytelnionego klienta zestawu SDK przy użyciu usługi `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Identyfikator klienta, identyfikator dzierżawy i adres URL wystąpienia można umieścić bezpośrednio w kodzie, jak pokazano powyżej, ale dobrym pomysłem jest, aby kod zamiast tego pozysł te wartości z pliku konfiguracji lub zmiennej środowiskowej.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Inne uwagi dotyczące uwierzytelniania Azure Functions

Zobacz [*How-to: Set up an Azure function for processing data*](how-to-create-azure-function.md) ( Jak skonfigurować funkcję platformy Azure do przetwarzania danych), aby uzyskać bardziej kompletny przykład, w tym opis niektórych ważnych opcji konfiguracji w kontekście funkcji.

Ponadto, aby używać uwierzytelniania w funkcji, pamiętaj, aby:
* [Włączanie tożsamości zarządzanej](../app-service/overview-managed-identity.md?tabs=dotnet)
* Używaj [zmiennych środowiskowych zgodnie](/sandbox/functions-recipes/environment-variables?tabs=csharp) z potrzebami
* Przypisz uprawnienia do aplikacji funkcji, które umożliwiają jej dostęp do Digital Twins API. Aby uzyskać więcej informacji na Azure Functions procesów, zobacz Jak skonfigurować funkcję [*platformy Azure do przetwarzania danych.*](how-to-create-azure-function.md)

## <a name="authenticate-across-tenants"></a>Uwierzytelnianie w wielu dzierżawach

Azure Digital Twins to usługa, która obsługuje tylko jedną  [dzierżawę usługi Azure Active Directory (Azure AD):](../active-directory/develop/quickstart-create-new-tenant.md)główną dzierżawę z subskrypcji, w której znajduje się Azure Digital Twins dzierżawy.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Jeśli musisz uzyskać dostęp do wystąpienia usługi Azure Digital Twins przy użyciu jednostki usługi lub konta użytkownika należącego do innej dzierżawy niż wystąpienie, możesz zażądać **tokenu** od dzierżawy "macierzyszej" wystąpienia usługi Azure Digital Twins. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

Możesz również określić dzierżawę domową w opcjach poświadczeń w kodzie. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Inne metody poświadczeń

Jeśli wyróżnione powyżej scenariusze uwierzytelniania nie obejmują potrzeb aplikacji, możesz eksplorować inne typy uwierzytelniania oferowane na platformie [**tożsamości firmy Microsoft.**](../active-directory/develop/v2-overview.md#getting-started) Dokumentacja tej platformy obejmuje dodatkowe scenariusze uwierzytelniania uporządkowane według typu aplikacji.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat sposobu działania zabezpieczeń w Azure Digital Twins:
* [*Pojęcia: zabezpieczenia dla Azure Digital Twins rozwiązań*](concepts-security.md)

Lub po skonfigurowaniu uwierzytelniania przejdź do tworzenia modeli i zarządzania nimi w swoim wystąpieniu:
* [*Instrukcja: zarządzanie modelami DTDL*](how-to-manage-model.md)