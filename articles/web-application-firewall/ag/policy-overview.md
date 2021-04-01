---
title: Omówienie zasad zapory aplikacji sieci Web platformy Azure (WAF)
description: Ten artykuł zawiera omówienie globalnych, dla poszczególnych witryn i zasad dotyczących identyfikatorów URI dla zapory aplikacji sieci Web (WAF).
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518740"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Omówienie zasad zapory aplikacji sieci Web platformy Azure (WAF)

Zasady zapory aplikacji sieci Web zawierają wszystkie ustawienia i konfiguracje WAF. Obejmuje to wykluczenia, reguły niestandardowe, reguły zarządzane i tak dalej. Te zasady są następnie skojarzone z bramą aplikacji (globalną), odbiornikiem (na lokację) lub regułą opartą na ścieżce (dla identyfikatora URI), aby zaczęła obowiązywać.

Nie ma żadnego limitu liczby zasad, które można utworzyć. Podczas tworzenia zasad musi być ona skojarzona z bramą aplikacji, aby zaczęła obowiązywać. Można je skojarzyć z dowolną kombinacją bram aplikacji, odbiorników i reguł opartych na ścieżce.

## <a name="global-waf-policy"></a>Globalne zasady WAF

W przypadku kojarzenia zasad WAFymi globalnie Każda witryna za Application Gateway WAF jest chroniona przy użyciu tych samych reguł zarządzanych, reguł niestandardowych, wykluczeń i innych skonfigurowanych ustawień.

Jeśli chcesz, aby pojedyncze zasady dotyczyły wszystkich lokacji, możesz skojarzyć zasady z bramą aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasad zapory aplikacji sieci Web dla Application Gateway](create-waf-policy-ag.md) , aby utworzyć i zastosować zasady WAF przy użyciu Azure Portal. 

## <a name="per-site-waf-policy"></a>Zasady WAF na lokację

Za pomocą zasad zapory aplikacji internetowej dla poszczególnych witryn można chronić wiele witryn z różnymi potrzebami dotyczącymi zabezpieczeń w ramach jednej zapory aplikacji internetowej przy użyciu zasad dla poszczególnych witryn. Jeśli na przykład za zaporą aplikacji internetowej znajduje się pięć witryn, można mieć pięć oddzielnych zasad zapory aplikacji internetowej (jedną dla każdego odbiornika), aby dostosować wykluczenia, reguły niestandardowe, zarządzane zestawy reguł oraz wszystkie inne ustawienia zapory aplikacji internetowej dla każdej witryny.

Załóżmy, że do bramy aplikacji są zastosowane zasady globalne. Należy zastosować różne zasady do odbiornika w tej bramie aplikacji. Zasady odbiornika teraz zaczynają obowiązywać tylko dla tego odbiornika. Globalne zasady bramy aplikacji nadal dotyczą wszystkich innych odbiorników i reguł opartych na ścieżce, do których nie są przypisane określone zasady.

## <a name="per-uri-policy"></a>Zasady dla identyfikatora URI

Aby jeszcze bardziej dostosowywać do poziomu identyfikatora URI, można skojarzyć zasady WAF z regułą opartą na ścieżce. W przypadku niektórych stron w jednej lokacji, które wymagają różnych zasad, można wprowadzić zmiany zasad WAF, które mają wpływ tylko na dany identyfikator URI. Może to dotyczyć płatności lub strony logowania, a także innych identyfikatorów URI, które potrzebują jeszcze bardziej szczegółowych zasad WAF niż w przypadku innych witryn za WAF.

Podobnie jak w przypadku zasad WAF dla poszczególnych witryn, bardziej szczegółowe zasady przesłaniają mniej szczegółowych zasad. Oznacza to, że zasady dotyczące identyfikatorów URI na mapie ścieżki URL przesłaniają wszystkie zasady dotyczące poszczególnych witryn lub globalnych WAF.

### <a name="example"></a>Przykład

Załóżmy, że masz trzy lokacje: contoso.com, fabrikam.com i adatum.com, które znajdują się za tą samą bramą aplikacji. Chcesz, aby WAF miało zastosowanie do wszystkich trzech witryn, ale potrzebujesz zabezpieczeń z adatum.com, ponieważ klienci odwiedzają, przeglądają i kupują produkty.

W razie potrzeby można zastosować do WAF zasady globalne z niektórymi podstawowymi ustawieniami, wykluczeniami lub regułami niestandardowymi, aby zatrzymać niektóre fałszywe pozytywne dane dotyczące blokowania ruchu. W takim przypadku nie ma potrzeby wykonywania globalnych reguł iniekcji SQL, ponieważ fabrikam.com i contoso.com są stronami statycznymi bez zaplecze języka SQL. W ten sposób można wyłączyć te reguły w ramach zasad globalnych.

Te zasady globalne są odpowiednie dla contoso.com i fabrikam.com, ale należy zachować ostrożność przy użyciu adatum.com, gdzie są obsługiwane informacje logowania i płatności. Zasady dotyczące poszczególnych lokacji można zastosować do odbiornika adatum i pozostawić uruchomione reguły SQL. Załóżmy również, że istnieje plik cookie blokujący ruch, dlatego można utworzyć wykluczenie dla tego pliku cookie, aby zatrzymać wynik fałszywie dodatni. 

Identyfikator URI adatum.com/payments to miejsce, w którym należy zachować ostrożność. Należy więc zastosować inne zasady dla tego identyfikatora URI i pozostawić wszystkie włączone reguły, a także usunąć wszystkie wykluczenia.

W tym przykładzie istnieją zasady globalne, które mają zastosowanie do dwóch lokacji. Masz zasady dla poszczególnych lokacji, które mają zastosowanie do jednej lokacji, a następnie zasady dotyczące identyfikatorów URI, które dotyczą jednej konkretnej reguły opartej na ścieżce. Zapoznaj się z tematem [Konfigurowanie zasad WAF dla poszczególnych lokacji przy użyciu Azure PowerShell](per-site-policies.md) dla odpowiedniego programu PowerShell.

## <a name="existing-waf-configurations"></a>Istniejące konfiguracje WAF

Wszystkie nowe ustawienia WAF zapory aplikacji sieci Web (reguły niestandardowe, konfiguracje zestawu reguł zarządzanych, wykluczenia itp.) istnieją w zasadach WAF. Jeśli masz istniejące WAF, te ustawienia mogą nadal istnieć w konfiguracji WAF. Aby uzyskać więcej informacji na temat przechodzenia do nowych zasad WAF, [dokonaj migracji konfiguracji WAF do zasad WAF](./migrate-policy.md). 


## <a name="next-steps"></a>Następne kroki

- [Tworzenie zasad dla poszczególnych witryn i identyfikatorów URI przy użyciu Azure PowerShell](per-site-policies.md).
