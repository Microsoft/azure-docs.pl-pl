---
title: Zabezpieczanie podmiotów usługi w Azure Active Directory
description: Znajdowanie, ocenianie i zabezpieczanie jednostek usługi.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604706"
---
# <a name="securing-service-principals"></a>Zabezpieczanie podmiotów usługi

Nazwa [główna usługi](../develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) to lokalna reprezentacja obiektu aplikacji w jednej dzierżawie lub katalogu.  Działa jako tożsamość wystąpienia aplikacji. Jednostki usługi definiują, kto może uzyskiwać dostęp do aplikacji i jakie zasoby mogą uzyskać dostęp do aplikacji. Jednostka usługi jest tworzona w każdej dzierżawie, w której jest używana aplikacja, i odwołuje się do globalnego obiektu aplikacji. Dzierżawca zabezpiecza logowanie jednostki usługi i dostęp do zasobów.  

### <a name="tenant-service-principal-relationships"></a>Dzierżawy — relacje głównej usługi
Aplikacja o pojedynczej dzierżawie ma tylko jedną jednostkę usługi w swojej dzierżawie głównej. Aplikacja sieci Web lub interfejs API z wieloma dzierżawcami wymaga nazwy głównej usługi w każdej dzierżawie. Jednostka usługi jest tworzona, gdy użytkownik z tej dzierżawy wyraził zgodę na użycie aplikacji lub interfejsu API. Ta zgoda tworzy relację "jeden do wielu" między aplikacją wielodostępną i skojarzoną z nią jednostkami usługi.

Aplikacja wielodostępna jest zaprojektowana w ramach jednej dzierżawy i ma wiele wystąpień w innych dzierżawcach. Większość aplikacji typu "oprogramowanie jako usługa" (SaaS) została zaprojektowana do obsługi wielu dzierżawców. Nazwy główne usług zapewniają odpowiednie stan zabezpieczeń dla aplikacji i jej użytkowników zarówno w przypadku pojedynczych dzierżawców, jak i wielu dzierżawców.

## <a name="applicationid-and-objectid"></a>Identyfikator aplikacji i identyfikator obiektu

Dana instancja aplikacji ma dwie odrębne właściwości: Identyfikator aplikacji (znany również jako ClientID) i ObjectID.

> [!NOTE] 
> Można się dowiedzieć, że warunki aplikacji i jednostki usługi są używane zamiennie w przypadku swobodnego odwoływania się do aplikacji w kontekście zadań związanych z uwierzytelnianiem. Są one jednak dwie różne reprezentacje aplikacji w usłudze Azure AD.
 

Identyfikator aplikacji reprezentuje aplikację globalną i jest taka sama dla wszystkich wystąpień aplikacji w dzierżawach. Identyfikator ObjectID jest unikatową wartością dla obiektu aplikacji i reprezentuje nazwę główną usługi. Podobnie jak w przypadku użytkowników, grup i innych zasobów, identyfikator ObjectID ułatwia unikatową identyfikację wystąpienia aplikacji w usłudze Azure AD.

Aby uzyskać szczegółowe informacje na temat tego tematu, zobacz [relacja główna aplikacji i usługi](../develop/app-objects-and-service-principals.md).

Możesz również utworzyć aplikację i jej obiekt główny usługi (ObjectID) w dzierżawie przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, Microsoft Graph, Azure Portal i innych narzędzi. 

![Zrzut ekranu przedstawiający nową rejestrację aplikacji z wyróżnionymi polami identyfikator aplikacji i identyfikator obiektu.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Istnieją dwa mechanizmy uwierzytelniania przy użyciu jednostek usługi — certyfikaty klienta i wpisy tajne klienta. 

![ Zrzut ekranu przedstawiający nową stronę aplikacji z wyróżnionymi obszarami certyfikaty i klucz tajny klienta.](./media/securing-service-accounts/secure-principal-certificates.png)

Certyfikaty są bezpieczniejsze: Użyj certyfikatów klienta, jeśli jest to możliwe. W przeciwieństwie do wpisów tajnych klienta certyfikaty klienta nie mogą być przypadkowo osadzone w kodzie. Użyj Azure Key Vault do zarządzania certyfikatami i wpisami tajnymi, gdy jest to możliwe, aby szyfrować następujące zasoby przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń:

* klucze uwierzytelniania

* klucze konta magazynu

* klucze szyfrowania danych

* pliki. pfx

* haśle 

Aby uzyskać więcej informacji na temat Azure Key Vault i sposobu korzystania z niego na potrzeby zarządzania certyfikatami i kluczami tajnymi, zobacz [Informacje o Azure Key Vault](../../key-vault/general/overview.md) i [przypisywaniu zasad dostępu Key Vault przy użyciu Azure Portal](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Wyzwania i środki zaradcze
W poniższej tabeli przedstawiono ograniczenia dotyczące problemów, które mogą wystąpić podczas korzystania z jednostek usługi.


| Wyzwania| Środki zaradcze |
| - | - |
| Przeglądy dostępu dla podmiotów usługi przypisanych do ról uprzywilejowanych.| Ta funkcja jest w wersji zapoznawczej i nie jest jeszcze szeroko dostępna. |
| Przegląda dostęp podmiotów głównych usługi| Ręczne sprawdzanie listy kontroli dostępu zasobu przy użyciu Azure Portal. |
| Za pośrednictwem uprawnionych podmiotów usługi| Podczas tworzenia kont usługi Automation lub jednostek usługi należy podać tylko uprawnienia wymagane dla tego zadania. Oceń istniejące jednostki usługi, aby sprawdzić, czy można ograniczyć uprawnienia. |
|Identyfikowanie modyfikacji nazw podmiotów usługi lub metod uwierzytelniania |Użyj skoroszytu raportów poufnych operacji, który może pomóc w ograniczeniu tego problemu. [Zobacz wyjaśnienie w tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Znajdowanie kont przy użyciu jednostek usługi
Uruchom następujące polecenia, aby znaleźć konta korzystające z nazw głównych usługi.

Korzystanie z interfejsu wiersza polecenia platformy Azure


`az ad sp list`

Korzystanie z programu PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Aby uzyskać więcej informacji, zobacz [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Oceń zabezpieczenia jednostki usługi

Aby ocenić zabezpieczenia podmiotów usługi, należy ocenić uprawnienia i magazyn poświadczeń.

Należy wyeliminować potencjalne wyzwania, korzystając z poniższych informacji.

|Wyzwania | Środki zaradcze|
| - | - |
| Wykrywaj użytkownika, który wyraził zgodę na aplikację z wieloma dzierżawcami, i Wykryj nielegalne dotacje dla aplikacji z wieloma dzierżawcami | Uruchom następujące środowisko PowerShell, aby znaleźć aplikacje wielodostępne.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Wyłącz wyrażenie zgody użytkownika. <br>Zezwalaj na zgodę użytkownika od zweryfikowanych wydawców na wybrane uprawnienia (zalecane) <br> Użyj dostępu warunkowego, aby blokować jednostki usługi z niezaufanych lokalizacji. Skonfiguruj je w kontekście użytkownika, a ich tokeny powinny służyć do wyzwalania nazwy głównej usługi.|
|Używanie zakodowanego klucza tajnego w skrypcie przy użyciu nazwy głównej usługi.|Użyj certyfikatu lub Azure Key Vault.|
|Śledzenie, kto używa certyfikatu lub klucza tajnego| Monitoruj logowania jednostki usługi przy użyciu dzienników logowania usługi Azure AD.|
Nie można zarządzać logowaniem nazw głównych usługi przy użyciu dostępu warunkowego.| Monitorowanie logowania przy użyciu dzienników logowania usługi Azure AD
| Domyślna rola RBAC platformy Azure to współautor. |Oceń potrzeby i stosuj rolę z najniższymi możliwymi uprawnieniami, aby sprostać potrzebom.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Przenoszenie z konta użytkownika do jednostki usługi  
Jeśli używasz konta użytkownika platformy Azure jako jednostki usługi, Oceń, czy możesz przejść do [tożsamości zarządzanej](../../app-service/overview-managed-identity.md?tabs=dotnet) lub nazwy głównej usługi. Jeśli nie możesz użyć tożsamości zarządzanej, Zainicjuj obsługę administracyjną jednostki usługi, która ma tylko wystarczające uprawnienia i zakres do uruchamiania wymaganych zadań. Można utworzyć jednostkę usługi przez [zarejestrowanie aplikacji](../develop/howto-create-service-principal-portal.md)lub przy użyciu [programu PowerShell](../develop/howto-authenticate-service-principal-powershell.md).

Korzystając z Microsoft Graph, zapoznaj się z dokumentacją określonego interfejsu API, [jak w tym przykładzie](/powershell/azure/create-azure-service-principal-azureps), i upewnij się, że typ uprawnień aplikacji jest wyświetlany jako obsługiwany.

## <a name="next-steps"></a>Następne kroki

**Aby dowiedzieć się więcej o jednostkach usługi:**

[Tworzenie nazwy głównej usługi](../develop/howto-create-service-principal-portal.md)

 [Monitoruj logowanie główne usługi](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Aby dowiedzieć się więcej na temat zabezpieczania kont usługi:**

[Wprowadzenie do kont usług platformy Azure](service-accounts-introduction-azure.md)

[Zabezpieczanie tożsamości zarządzanych](service-accounts-managed-identities.md)

[Zarządzanie kontami usług platformy Azure](service-accounts-governing-azure.md)

[Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)
