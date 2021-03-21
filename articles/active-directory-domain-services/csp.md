---
title: Azure AD Domain Services dla dostawców rozwiązań w chmurze | Microsoft Docs
description: Dowiedz się, jak włączyć i zarządzać Azure Active Directory Domain Services domenami zarządzanymi dla dostawców rozwiązań w chmurze platformy Azure
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619903"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Domain Services wdrażania i zarządzania dla dostawców rozwiązań w chmurze platformy Azure

Dostawcy rozwiązań w chmurze platformy Azure są programem dla partnerów firmy Microsoft i udostępniają kanał licencji dla różnych usług w chmurze firmy Microsoft. Dostawca CSP platformy Azure umożliwia partnerom Zarządzanie sprzedażą, własną relacją rozliczeń, obsługę techniczną i rozliczeniami oraz stanowić pojedynczy punkt kontaktu klienta. Ponadto dostawca CSP platformy Azure oferuje pełny zestaw narzędzi, w tym Portal samoobsługowy i dołączone interfejsy API. Te narzędzia umożliwiają partnerom programu CSP łatwe inicjowanie obsługi zasobów platformy Azure i zarządzanie nimi oraz zapewnianie rozliczeń dla klientów i ich subskrypcji.

[Portal Centrum partnerskiego](/partner-center/azure-plan-lp) jest punktem wejścia dla wszystkich partnerów programu Azure CSP i oferuje bogate możliwości zarządzania klientami, zautomatyzowane przetwarzanie i wiele innych. Partnerzy CSP platformy Azure mogą korzystać z funkcji Centrum partnerskiego za pomocą interfejsu użytkownika opartego na sieci Web lub programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak model CSP działa na wysokim poziomie. Tutaj firma Contoso ma dzierżawę usługi Azure Active Directory (Azure AD). Mają one partnerstwo z dostawcą usług kryptograficznych, który wdraża zasoby i zarządza nimi w ramach subskrypcji dostawcy CSP platformy Azure. Firma Contoso może również mieć regularne (bezpośrednie) subskrypcje platformy Azure, które są rozliczane bezpośrednio do firmy Contoso.

![Omówienie modelu CSP](./media/csp/csp_model_overview.png)

Dzierżawca partnera CSP ma trzy specjalne grupy agentów — agentów *administracyjnych* , agentów *pomocy technicznej* i agentów *sprzedaży* .

Grupa agenci *administracyjni* jest przypisana do roli Administrator dzierżawy w dzierżawie usługi Azure AD firmy Contoso. W związku z tym użytkownik należący do grupy agentów administracyjnych partnera CSP ma uprawnienia administratora dzierżawy w dzierżawie usługi Azure AD firmy Contoso.

Gdy Partner CSP inicjuje subskrypcję dostawcy usług kryptograficznych platformy Azure dla firmy Contoso, jej grupy agentów administracyjnych są przypisywane do roli właściciela tej subskrypcji. W związku z tym agenci administracyjni dostawcy usług kryptograficznych mają uprawnienia wymagane do udostępniania zasobów platformy Azure, takich jak maszyny wirtualne, sieci wirtualne i Azure AD Domain Services w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [Omówienie usług kryptograficznych platformy Azure](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Zalety korzystania z usługi Azure AD DS w ramach subskrypcji dostawcy usług kryptograficznych platformy Azure

Azure Active Directory Domain Services (AD DS platformy Azure) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, protokół LDAP, uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory Domain Services. W ciągu dekad wiele aplikacji zostało skompilowanych do pracy z usługą AD przy użyciu tych funkcji. Wielu niezależnych dostawców oprogramowania (ISV) ma aplikacje skompilowane i wdrożone w siedzibie klientów. Te aplikacje trudno obsługiwać, ponieważ często wymagają dostępu do różnych środowisk, w których aplikacje są wdrażane. Subskrypcje dostawcy CSP platformy Azure pozwalają uprościć alternatywę od skalowalności i elastyczności platformy Azure.

Usługa Azure AD DS obsługuje subskrypcje dostawcy CSP platformy Azure. Aplikację można wdrożyć w ramach subskrypcji dostawcy CSP platformy Azure powiązanej z dzierżawą usługi Azure AD danego klienta. W związku z tym pracownicy (personel pomocy technicznej) mogą zarządzać maszynami wirtualnymi, na których aplikacja jest wdrażana i obsługiwać je, korzystając z poświadczeń firmowych w organizacji.

Możesz również wdrożyć domenę zarządzaną platformy Azure AD DS w dzierżawie usługi Azure AD klienta. Aplikacja zostanie połączona z domeną zarządzaną przez klienta. Możliwości w aplikacji korzystające z protokołu Kerberos/NTLM, LDAP lub [interfejsu API System. DirectoryServices](/dotnet/api/system.directoryservices) bezproblemowo współpracują z domeną klienta. Klienci końcowi korzystają z używania aplikacji jako usługi, nie trzeba martwić się o utrzymanie infrastruktury, w której aplikacja jest wdrażana.

Wszystkie opłaty za zasoby platformy Azure zużywane w ramach tej subskrypcji, w tym Azure AD DS, są naliczane z powrotem. Zapewniasz pełną kontrolę nad relacją z klientem, gdy chodzi o sprzedaż, rozliczenia, pomoc techniczną itp. Dzięki elastyczności platformy CSP platformy Azure niewielki zespół agentów pomocy technicznej może obsługiwać wielu klientów, którzy mają wdrożone wystąpienia aplikacji.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modele wdrażania dostawcy CSP dla usługi Azure AD DS

Istnieją dwa sposoby użycia usługi Azure AD DS z subskrypcją dostawcy usług kryptograficznych platformy Azure. Wybierz jedną z nich na podstawie zagadnień związanych z bezpieczeństwem i prostotą posiadanych przez klientów.

### <a name="direct-deployment-model"></a>Model wdrażania bezpośredniego

W tym modelu wdrożenia usługa Azure AD DS jest włączona w sieci wirtualnej należącej do subskrypcji dostawcy usług kryptograficznych platformy Azure. Agenci administracyjni dostawcy usług kryptograficznych mają następujące uprawnienia:

* Uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD klienta.
* Uprawnienia *właściciela subskrypcji* w ramach subskrypcji dostawcy CSP platformy Azure.

![Model wdrażania bezpośredniego](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrożenia agenci administracyjni dostawcy usług kryptograficznych mogą administrować tożsamościami dla klienta. Ci agenci administracyjni mogą wykonywać zadania, takie jak inicjowanie obsługi nowych użytkowników lub grup lub Dodawanie aplikacji w ramach dzierżawy usługi Azure AD klienta.

Ten model wdrażania może być dostosowany do mniejszych organizacji, które nie mają dedykowanego administratora tożsamości ani woli, aby partner CSP mogli administrować tożsamościami w ich imieniu.

### <a name="peered-deployment-model"></a>Model wdrażania równorzędnego

W tym modelu wdrożenia usługa Azure AD DS jest włączona w ramach sieci wirtualnej należącej do klienta — bezpośrednia subskrypcja platformy Azure płacona przez klienta. Partner programu CSP może wdrażać aplikacje w obrębie sieci wirtualnej należącej do subskrypcji dostawcy CSP klienta. Sieci wirtualne można następnie połączyć za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure.

W tym wdrożeniu obciążenia i aplikacje wdrożone przez partnera CSP w ramach subskrypcji dostawcy usług kryptograficznych platformy Azure mogą łączyć się z domeną zarządzaną klienta, która została zainicjowana w bezpośredniej subskrypcji klienta platformy Azure.

![Model wdrażania równorzędnego](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania umożliwia rozdzielenie uprawnień i umożliwia agentom pomocy technicznej partnera usług kryptograficznych administrowanie subskrypcją platformy Azure oraz wdrażanie zasobów i zarządzanie nimi. Jednak agenci działu pomocy technicznej partnera CSP nie muszą mieć uprawnień administratora globalnego w katalogu usługi Azure AD klienta. Administratorzy tożsamości klienta mogą nadal zarządzać tożsamościami dla swojej organizacji.

Ten model wdrażania może być dostosowany do scenariuszy, w których dostawca ISV udostępnia hostowaną wersję aplikacji lokalnej, która również musi nawiązać połączenie z usługą Azure AD klienta.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrowanie usługą Azure AD DS w subskrypcjach programu CSP

W przypadku administrowania domeną zarządzaną w ramach subskrypcji dostawcy CSP platformy Azure obowiązują następujące ważne zagadnienia:

* **Agenci administracyjni dostawcy usług kryptograficznych mogą udostępniać domenę zarządzaną przy użyciu swoich poświadczeń:** Usługa Azure AD DS obsługuje subskrypcje dostawcy CSP platformy Azure. Użytkownicy należący do grupy agentów administracyjnych partnera CSP mogą inicjować nową domenę zarządzaną.

* **Dostawcy usług kryptograficznych mogą tworzyć skrypty tworzenia nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** Aby uzyskać szczegółowe informacje [, zobacz Jak włączyć usługę Azure AD DS przy użyciu programu PowerShell](powershell-create-instance.md) .

* **Agenci administracyjni dostawcy usług kryptograficznych nie mogą wykonywać bieżących zadań zarządzania w domenie zarządzanej przy użyciu ich poświadczeń:** Użytkownicy administracyjni dostawcy CSP nie mogą wykonywać rutynowych zadań zarządzania w domenie zarządzanej przy użyciu ich poświadczeń. Ci użytkownicy są użytkownikami zewnętrznymi dzierżawy usługi Azure AD klienta, a ich poświadczenia nie są dostępne w ramach dzierżawy usługi Azure AD klienta. Usługa Azure AD DS nie ma dostępu do skrótów haseł protokołu Kerberos i NTLM dla tych użytkowników, dlatego nie można uwierzytelnić użytkowników w domenach zarządzanych.

  > [!WARNING]
  > Musisz utworzyć konto użytkownika w katalogu klienta, aby wykonywać bieżące zadania administracyjne w domenie zarządzanej.
  >
  > Nie można zalogować się do domeny zarządzanej przy użyciu poświadczeń użytkownika administratora dostawcy usług kryptograficznych. Aby to zrobić, Użyj poświadczeń konta użytkownika należącego do dzierżawy usługi Azure AD klienta. Te poświadczenia są potrzebne do wykonywania zadań, takich jak łączenie maszyn wirtualnych z domeną zarządzaną, administrowanie systemem DNS lub administrowanie zasady grupy.

* **Konto użytkownika utworzone na potrzeby ciągłej administracji należy dodać do grupy *administratorów kontrolera domeny usługi AAD* :** Grupa *administratorzy kontrolera domeny usługi AAD* ma uprawnienia do wykonywania niektórych delegowanych zadań administracyjnych w domenie zarządzanej. Te zadania obejmują Konfigurowanie systemu DNS, tworzenie jednostek organizacyjnych i administrowanie zasadami grupy.
    
    Aby partner programu CSP wykonywał te zadania w domenie zarządzanej, należy utworzyć konto użytkownika w ramach dzierżawy usługi Azure AD klienta. Poświadczenia dla tego konta muszą być udostępniane agentom administracyjnym partnera programu CSP. Ponadto to konto użytkownika należy dodać do grupy *administratorów kontrolera domeny usługi AAD* , aby umożliwić wykonywanie zadań konfiguracyjnych w domenie zarządzanej za pomocą tego konta użytkownika.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [zarejestruj się w programie CSP platformy Azure](/partner-center/enrolling-in-the-csp-program). Następnie można włączyć Azure AD Domain Services przy użyciu [Azure Portal](tutorial-create-instance.md) lub [Azure PowerShell](powershell-create-instance.md).