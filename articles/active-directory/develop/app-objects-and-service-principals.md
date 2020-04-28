---
title: Aplikacje & nazwy główne usługi w usłudze Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Informacje o relacji między obiektami obiektów głównych aplikacji i usług w Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: a636ff15da09bcf1891618d65270376f26fd3239
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885603"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory

Czasami znaczenie terminu "aplikacja" może być interpretowane w kontekście Azure Active Directory (Azure AD). W tym artykule wyjaśniono koncepcje i konkretne aspekty integracji aplikacji usługi Azure AD z ilustracją rejestracji i zgody dla [aplikacji wielodostępnych](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Omówienie

Aplikacja zintegrowana z usługą Azure AD ma konsekwencje wykraczające poza aspekt oprogramowania. "Aplikacja" jest często używana jako termin koncepcyjny, odnoszący się do nie tylko oprogramowania aplikacji, ale także rejestrację i rolę usługi Azure AD w ramach uwierzytelniania/autoryzacji "konwersacje" w czasie wykonywania.

Zgodnie z definicją aplikacja może działać w następujących rolach:

- Rola [klienta](developer-glossary.md#client-application) (zużywanie zasobu)
- Rola [serwera zasobów](developer-glossary.md#resource-server) (Uwidacznianie interfejsów API klientom)
- Rola klienta i serwer zasobów

[Przepływ przyznania autoryzacji OAuth 2,0](developer-glossary.md#authorization-grant) definiuje protokół konwersacji, który umożliwia klientowi/zasobowi dostęp/ochronę danych zasobów.

W poniższych sekcjach opisano sposób, w jaki model aplikacji usługi Azure AD reprezentuje aplikację w czasie projektowania i w czasie wykonywania.

## <a name="application-registration"></a>Rejestracja aplikacji

Po zarejestrowaniu aplikacji usługi Azure AD w [Azure Portal][AZURE-Portal]są tworzone dwa obiekty w dzierżawie usługi Azure AD:

- Obiekt aplikacji i
- Obiekt jednostki usługi

### <a name="application-object"></a>Obiekt aplikacji

Aplikacja usługi Azure AD jest definiowana przez jeden i tylko obiekt aplikacji, który znajduje się w dzierżawie usługi Azure AD, w której zarejestrowano aplikację, znanej jako dzierżawa "główna" aplikacji. [Jednostka aplikacji][MS-Graph-App-Entity] Microsoft Graph definiuje schemat właściwości obiektu aplikacji.

### <a name="service-principal-object"></a>Obiekt główny usługi

Aby uzyskać dostęp do zasobów zabezpieczonych przez dzierżawę usługi Azure AD, jednostka wymagająca dostępu musi być reprezentowana przez podmiot zabezpieczeń. Dotyczy to zarówno użytkowników (głównej nazwy użytkownika), jak i aplikacji (nazwy głównej usługi).

Podmiot zabezpieczeń definiuje zasady dostępu i uprawnienia dla użytkownika/aplikacji w dzierżawie usługi Azure AD. Umożliwia to korzystanie z podstawowych funkcji, takich jak uwierzytelnianie użytkownika/aplikacji podczas logowania, oraz autoryzacja w trakcie dostępu do zasobów.

Gdy aplikacja uzyskuje uprawnienia dostępu do zasobów w dzierżawie (po rejestracji lub [zgodzie](developer-glossary.md#consent)), tworzony jest obiekt jednostki usługi. Jednostka Microsoft Graph [serviceprincipal][MS-Graph-Sp-Entity] definiuje schemat właściwości obiektu głównego usługi.

### <a name="application-and-service-principal-relationship"></a>Relacja aplikacji i jednostki usługi

Rozważmy obiekt aplikacji jako *globalną* reprezentację aplikacji do użycia we wszystkich dzierżawcach i nazwę główną usługi jako reprezentację *lokalną* do użycia w określonej dzierżawie.

Obiekt aplikacji służy jako szablon, z którego *pochodzą* typowe i domyślne właściwości do stosowania podczas tworzenia odpowiednich obiektów jednostki usługi. W związku z tym obiekt aplikacji ma 1:1 relację z aplikacją oprogramowania i 1: wiele relacji z odpowiadającymi im obiektami głównych usług.

Należy utworzyć jednostkę usługi w każdej dzierżawie, w której jest używana aplikacja, umożliwiając jej ustalenie tożsamości logowania i/lub dostęp do zasobów zabezpieczonych przez dzierżawcę. Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi (w swojej głównej dzierżawie) utworzoną do użycia podczas rejestracji aplikacji i mającą na to zgodę. Wielodostępna aplikacja sieci Web/interfejs API ma także nazwę główną usługi utworzoną w każdej dzierżawie, w której użytkownik z tej dzierżawy wyraził zgodę na jego użycie.

> [!NOTE]
> Wszelkie zmiany wprowadzone w obiekcie aplikacji również są odzwierciedlone w jego obiekcie głównym usługi tylko w dzierżawie głównym aplikacji (dzierżawie, w której został zarejestrowany). W przypadku aplikacji wielodostępnych zmiany w obiekcie aplikacji nie są odzwierciedlane w obiektach głównych usługi dzierżawców klientów, dopóki dostęp nie zostanie usunięty za pomocą [panelu dostępu do aplikacji](https://myapps.microsoft.com) i ponownie udzielony.
>
> Należy również zauważyć, że natywne aplikacje są domyślnie zarejestrowane jako wielodostępne.

## <a name="example"></a>Przykład

Na poniższym diagramie przedstawiono relację między obiektem aplikacji aplikacji i odpowiednimi obiektami głównej usługi w kontekście przykładowej aplikacji wielodostępnej o nazwie **HR**. W tym przykładowym scenariuszu istnieją trzy dzierżawy usługi Azure AD:

- **Adatum** — dzierżawca używany przez firmę, która opracowała **aplikację kadrową**
- **Contoso** — dzierżawa używana przez organizację firmy Contoso, która jest klientem **aplikacji kadrowej**
- **Fabrikam** — dzierżawca używany przez organizację firmy Fabrikam, która również korzysta z **aplikacji kadrowej**

![Relacja między obiektem aplikacji a obiektem głównym usługi](./media/app-objects-and-service-principals/application-objects-relationship.svg)

W tym przykładowym scenariuszu:

| Krok | Opis |
|------|-------------|
| 1    | Jest procesem tworzenia obiektów głównych aplikacji i usług w dzierżawie głównej aplikacji. |
| 2    | Gdy administratorzy Contoso i fabrikam ukończyją zgodę, obiekt główny usługi jest tworzony w dzierżawie usługi Azure AD swojej firmy i ma przypisane uprawnienia przyznane przez administratora. Należy również pamiętać, że aplikacja KADRowa mogła zostać skonfigurowana/zaprojektowana tak, aby zezwalać użytkownikom na zgodę na użycie indywidualnych. |
| 3    | Dzierżawy odbiorców aplikacji KADRowej (Contoso i fabrikam) mają własne obiekty główne usługi. Każdy z nich reprezentuje użycie wystąpienia aplikacji w czasie wykonywania, zgodnie z uprawnieniami przydzielonymi przez odpowiedniego administratora. |

## <a name="next-steps"></a>Następne kroki

- Za pomocą [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) można wysyłać zapytania do obiektów głównych aplikacji i usług.
- Dostęp do obiektu aplikacji aplikacji można uzyskać za pomocą interfejsu API Microsoft Graph, edytora manifestu aplikacji [Azure Portal][AZURE-Portal] lub [poleceń cmdlet programu POWERSHELL usługi Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), które są reprezentowane przez [jednostkę aplikacji][MS-Graph-App-Entity]OData.
- Dostęp do obiektu głównego usługi aplikacji można uzyskać za pomocą interfejsu API Microsoft Graph lub [poleceń cmdlet programu PowerShell usługi Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)reprezentowanego przez jego [jednostkę główną][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
