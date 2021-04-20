---
title: Aplikacje & jednostki usługi w usłudze Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o relacji między obiektami aplikacji i jednostki usługi w Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727064"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory

W tym artykule opisano rejestrację aplikacji, obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory: czym są, jak są używane i jak są ze sobą powiązane. Przedstawiono również przykładowy scenariusz z wieloma dzierżawami w celu zilustrowania relacji między obiektem aplikacji aplikacji i odpowiadającymi im obiektami jednostki usługi.

## <a name="application-registration"></a>Rejestracja aplikacji
Aby delegować funkcje zarządzania tożsamościami i dostępem do usługi Azure AD, aplikacja musi być zarejestrowana w dzierżawie usługi Azure [AD.](developer-glossary.md#tenant) Podczas rejestrowania aplikacji w usłudze Azure AD tworzysz konfigurację tożsamości dla aplikacji, która umożliwia jej integrację z usługą Azure AD. Podczas rejestrowania aplikacji w u użytkownika Azure Portal możesz wybrać, czy jest [to][AZURE-Portal]pojedyncza dzierżawa (dostępna tylko w Twojej dzierżawie), czy wielodostępna (dostępna w innych dzierżawach) i opcjonalnie ustawić adres URI przekierowania (do którego jest wysyłany token dostępu).

Instrukcje krok po kroku dotyczące rejestrowania aplikacji można znaleźć w przewodniku Szybki start dotyczącym [rejestrowania aplikacji.](quickstart-register-app.md)

Po zakończeniu rejestracji aplikacji masz globalnie unikatowe wystąpienie aplikacji (obiekt aplikacji [),](#application-object)które znajduje się w twojej głównej dzierżawie lub katalogu.  Masz również globalnie unikatowy identyfikator aplikacji (identyfikator aplikacji lub klienta).  W portalu można następnie dodawać wpisy tajne lub certyfikaty i zakresy, aby aplikacja działała, dostosowywać znakowanie aplikacji w oknie dialogowym logowania i nie tylko.

Jeśli zarejestrujesz aplikację w portalu, w dzierżawie głównej zostanie automatycznie utworzony obiekt aplikacji oraz obiekt jednostki usługi.  Jeśli zarejestrujesz/utworzysz aplikację przy użyciu interfejsów API Microsoft Graph, utworzenie obiektu jednostki usługi jest osobnym krokiem.

## <a name="application-object"></a>Obiekt aplikacji
Aplikacja usługi Azure AD jest definiowana przez jej jeden i jedyny obiekt aplikacji, który znajduje się w dzierżawie usługi Azure AD, w której aplikacja została zarejestrowana (znana jako "główna" dzierżawa aplikacji).  Obiekt aplikacji jest używany jako szablon lub strategia do tworzenia co najmniej jednego obiektu jednostki usługi.  Jednostkę usługi tworzy się w każdej dzierżawie, w której jest używana aplikacja. Podobnie jak w przypadku klasy w programowaniu obiektowym, obiekt aplikacji ma pewne właściwości statyczne, które są stosowane do wszystkich utworzonych podmiotów usługi (lub wystąpień aplikacji).

Obiekt aplikacji opisuje trzy aspekty aplikacji: sposób, w jaki usługa może wystawiać tokeny w celu uzyskania dostępu do aplikacji, zasoby, do których aplikacja może potrzebować dostępu, oraz akcje, które może podjąć aplikacja.

Blok **Rejestracje aplikacji** w Azure Portal [służy][AZURE-Portal] do listy obiektów aplikacji w dzierżawie głównej i zarządzania nimi.

![Rejestracje aplikacji blok](./media/app-objects-and-service-principals/app-registrations-blade.png)

Jednostka Microsoft Graph [Application][MS-Graph-App-Entity] definiuje schemat właściwości obiektu aplikacji.

## <a name="service-principal-object"></a>Obiekt jednostki usługi
Aby uzyskać dostęp do zasobów zabezpieczonych przez dzierżawę usługi Azure AD, jednostka wymagająca dostępu musi być reprezentowana przez podmiot zabezpieczeń. To wymaganie dotyczy zarówno użytkowników (jednostki użytkownika), jak i aplikacji (jednostki usługi). Podmiot zabezpieczeń definiuje zasady dostępu i uprawnienia dla użytkownika/aplikacji w dzierżawie usługi Azure AD. Umożliwia to korzystanie z podstawowych funkcji, takich jak uwierzytelnianie użytkownika/aplikacji podczas logowania i autoryzacja podczas dostępu do zasobów.

Istnieją trzy typy jednostki usługi: aplikacja, tożsamość zarządzana i starsza wersja.

Pierwszym typem jednostki usługi jest lokalna reprezentacja lub wystąpienie aplikacji globalnego obiektu aplikacji w jednej dzierżawie lub katalogu. W tym przypadku jednostką usługi jest konkretne wystąpienie utworzone na podstawie obiektu aplikacji i dziedziczy niektóre właściwości z tego obiektu aplikacji. W każdej dzierżawie jest tworzona jednostka usługi, w której jest używana aplikacja, i odwołuje się do globalnie unikatowego obiektu aplikacji.  Obiekt jednostki usługi definiuje, co aplikacja może faktycznie zrobić w określonej dzierżawie, kto może uzyskać dostęp do aplikacji i do jakich zasobów aplikacja może uzyskać dostęp.

Gdy aplikacja ma uprawnienia dostępu do zasobów w dzierżawie (po rejestracji lub [zgody),](developer-glossary.md#consent)tworzony jest obiekt jednostki usługi. Obiekty jednostki usługi można również tworzyć w dzierżawie przy użyciu interfejsu [Azure PowerShell,](howto-authenticate-service-principal-powershell.md)interfejsu wiersza polecenia platformy [Azure,](/cli/azure/create-an-azure-service-principal-azure-cli) [Microsoft Graph,](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http)Azure Portal [i][AZURE-Portal]innych narzędzi. W przypadku korzystania z portalu jednostkę usługi tworzy się automatycznie podczas rejestrowania aplikacji.

Drugi typ jednostki usługi jest używany do reprezentowania tożsamości [zarządzanej](/azure/active-directory/managed-identities-azure-resources/overview). Tożsamości zarządzane eliminują potrzebę zarządzania poświadczeniami przez deweloperów. Tożsamości zarządzane zapewniają tożsamość dla aplikacji do użycia podczas nawiązywania połączenia z zasobami, które obsługują uwierzytelnianie usługi Azure AD. Po włączeniu tożsamości zarządzanej w dzierżawie jest tworzona jednostka usługi reprezentująca tożsamość zarządzaną. Jednostkom usługi reprezentującym tożsamości zarządzane można przyznać dostęp i uprawnienia, ale nie można ich bezpośrednio aktualizować ani modyfikować.

Trzeci typ jednostki usługi reprezentuje starszą aplikację (aplikację utworzoną przed wprowadzeniem lub utworzeniem rejestracji aplikacji za pomocą starszych wersji). Starsza nazwa główna usługi może mieć poświadczenia, nazwy główne usługi, adresy URL odpowiedzi i inne właściwości, które są edytowalne przez autoryzowanego użytkownika, ale nie mają skojarzonej rejestracji aplikacji. Jednostki usługi można używać tylko w dzierżawie, w której została utworzona.

Jednostka Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definiuje schemat właściwości obiektu jednostki usługi.

Blok **Aplikacje dla przedsiębiorstw** w portalu służy do listy i zarządzania jednostkami usługi w dzierżawie. Możesz zobaczyć uprawnienia jednostki usługi, uprawnienia wyrażane przez użytkownika, to, którzy użytkownicy wyraziły zgodę, informacje logowania i inne informacje.

![Blok Aplikacje dla przedsiębiorstw](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Relacja między obiektami aplikacji i jednostkami usługi

Obiekt aplikacji jest *globalną reprezentacją* aplikacji do użycia we wszystkich dzierżawach, a jednostką usługi jest lokalna reprezentacja do użycia w określonej dzierżawie. 

Obiekt aplikacji służy jako szablon, z którego *pochodzą* typowe i domyślne właściwości do stosowania podczas tworzenia odpowiednich obiektów jednostki usługi. W związku z tym obiekt aplikacji ma relację 1:1 z aplikacją i relację 1:many z odpowiednimi obiektami jednostki usługi.

W każdej dzierżawie, w której jest używana aplikacja, należy utworzyć jednostkę usługi, umożliwiając jej ustanowienie tożsamości do logowania i/lub dostępu do zasobów zabezpieczonych przez dzierżawę. Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi (w swojej głównej dzierżawie) utworzoną do użycia podczas rejestracji aplikacji i mającą na to zgodę. Aplikacja wielodostępna ma również jednostkę usługi utworzoną w każdej dzierżawie, w której użytkownik z tej dzierżawy wyraził zgodę na jej używanie.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Konsekwencje modyfikowania i usuwania aplikacji
Wszelkie zmiany wprowadzone w obiekcie aplikacji są również odzwierciedlane w obiekcie jednostki usługi tylko w dzierżawie głównej aplikacji (dzierżawie, w której została zarejestrowana). Oznacza to, że usunięcie obiektu aplikacji spowoduje również usunięcie obiektu jednostki usługi dzierżawy głównej.  Jednak przywrócenie tego obiektu aplikacji nie spowoduje przywrócenia odpowiadającej mu jednostki usługi. W przypadku aplikacji wielodostępnych zmiany obiektu aplikacji nie są odzwierciedlane w obiektach jednostki usługi dzierżawców klienta, dopóki dostęp nie zostanie usunięty za pośrednictwem usługi [Application Panel dostępu](https://myapps.microsoft.com) i udzielony ponownie.

## <a name="example"></a>Przykład

Na poniższym diagramie przedstawiono relację między obiektem aplikacji aplikacji i odpowiadającymi im obiektami jednostki usługi w kontekście przykładowej aplikacji wielodostępnej o nazwie **APLIKACJA HR**. W tym przykładzie istnieją trzy dzierżawy usługi Azure AD:

- **Adatum** — dzierżawa używana przez firmę, która opracowała **aplikację hr**
- **Contoso** — dzierżawa używana przez organizację Contoso, która jest konsumentem aplikacji **HR**
- **Fabrikam** — dzierżawa używana przez organizację Fabrikam, która również korzysta z **aplikacji HR**

![Relacja między obiektem aplikacji a obiektem jednostki usługi](./media/app-objects-and-service-principals/application-objects-relationship.svg)

W tym przykładzie scenariusz:

| Krok | Opis |
|------|-------------|
| 1    | Jest procesem tworzenia obiektów aplikacji i jednostki usługi w dzierżawie głównej aplikacji. |
| 2    | Gdy administratorzy firmy Contoso i Fabrikam wyrazić zgodę, w dzierżawie usługi Azure AD firmy zostanie utworzony obiekt jednostki usługi i przypisane uprawnienia przyznane przez administratora. Należy również pamiętać, że aplikację HR można skonfigurować/zaprojektować tak, aby umożliwić użytkownikom wyrażanie zgody do użytku indywidualnego. |
| 3    | Dzierżawy konsumentów aplikacji HR (Contoso i Fabrikam) mają własny obiekt jednostki usługi. Każdy z nich reprezentuje użycie wystąpienia aplikacji w czasie wykonywania i podlega uprawnieńom wyrażanych przez odpowiedniego administratora. |

## <a name="next-steps"></a>Następne kroki

- Możesz użyć Eksploratora [Microsoft Graph,](https://developer.microsoft.com/graph/graph-explorer) aby odpytować zarówno obiekty aplikacji, jak i jednostki usługi.
- Dostęp do obiektu aplikacji aplikacji można uzyskać przy użyciu interfejsu API usługi Microsoft Graph, edytora manifestu aplikacji usługi [Azure Portal][AZURE-Portal] lub poleceń [cmdlet](/powershell/azure/)programu PowerShell usługi Azure AD reprezentowanych przez jednostkę aplikacji [OData.][MS-Graph-App-Entity]
- Dostęp do obiektu jednostki usługi aplikacji można uzyskać za pośrednictwem interfejsu API usługi Microsoft Graph lub poleceń [cmdlet](/powershell/azure/)programu PowerShell usługi Azure AD reprezentowanych przez jednostkę OData [ServicePrincipal.][MS-Graph-Sp-Entity]

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
