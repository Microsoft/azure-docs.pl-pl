---
title: Ochrona hasłem w usłudze Azure AD — Azure Active Directory
description: Zablokuj słabe hasła w lokalnych środowiskach Active Directory Domain Services przy użyciu funkcji ochrony hasłem w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743925"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Wymuś ochronę lokalnego hasła usługi Azure AD dla Active Directory Domain Services

Ochrona hasłem w usłudze Azure AD wykrywa i blokuje znane hasła oraz ich warianty, a także może blokować dodatkowe słabe warunki, które są specyficzne dla organizacji. Lokalne wdrożenie ochrony hasłem usługi Azure AD używa tych samych globalnych i niestandardowych list haseł, które są przechowywane w usłudze Azure AD, i sprawdza, czy lokalne zmiany hasła, ponieważ usługa Azure AD jest w przypadku zmian opartych na chmurze. Te testy są wykonywane podczas zmiany hasła i zdarzeń resetowania hasła na kontrolerach domeny lokalnych Active Directory Domain Services (AD DS).

## <a name="design-principles"></a>Zasady projektowania

Ochrona hasłem usługi Azure AD jest zaprojektowana z uwzględnieniem następujących zasad:

* Kontrolery domeny nigdy nie muszą komunikować się bezpośrednio z Internetem.
* Na kontrolerach domeny nie są otwierane żadne nowe porty sieciowe.
* Nie są wymagane żadne zmiany schematu AD DS. Oprogramowanie używa istniejącego *kontenera* AD DS i obiektów schematu *serviceConnectionPoint* .
* Nie jest wymagany minimalny AD DS domeny ani poziomu funkcjonalności lasu (DFL/FFL).
* Oprogramowanie nie tworzy ani nie wymaga kont w domenach AD DS, które chroni.
* Hasła w postaci zwykłego tekstu użytkownika nigdy nie opuszczają kontrolera domeny, podczas operacji weryfikacji hasła lub w innym czasie.
* Oprogramowanie nie jest zależne od innych funkcji usługi Azure AD. Na przykład funkcja synchronizacji skrótów haseł w usłudze Azure AD (PHS) nie jest powiązana ani wymagana do ochrony hasłem usługi Azure AD.
* Wdrożenie przyrostowe jest obsługiwane, ale zasady haseł są wymuszane tylko wtedy, gdy jest zainstalowany agent kontrolera domeny (Agent DC).

## <a name="incremental-deployment"></a>Wdrożenie przyrostowe

Ochrona hasłem w usłudze Azure AD obsługuje wdrożenie przyrostowe w wielu kontrolerach domeny w domenie AD DS. Ważne jest, aby zrozumieć, co to naprawdę znaczy i jakie są kompromisy.

Oprogramowanie agenta DC ochrony hasłem w usłudze Azure AD może sprawdzać poprawność haseł tylko wtedy, gdy są zainstalowane na kontrolerze domeny i tylko dla zmian haseł, które są wysyłane do tego kontrolera domeny. Nie można kontrolować, które kontrolery domeny są wybierane przez komputery klienckie z systemem Windows na potrzeby przetwarzania zmian hasła użytkownika. W celu zagwarantowania spójności spójnej i uniwersalnego egzekwowania zabezpieczeń ochrony hasłem w usłudze Azure AD należy zainstalować oprogramowanie agenta kontrolera domeny na wszystkich kontrolerach domeny w domenie.

Wiele organizacji chce uważnie przetestować ochronę hasłem usługi Azure AD na podzestawie swoich kontrolerów domeny przed pełnym wdrożeniem. Aby zapewnić obsługę tego scenariusza, ochrona hasłem w usłudze Azure AD obsługuje częściowe wdrażanie. Oprogramowanie agenta kontrolera domeny w danym kontrolerze domeny aktywnie weryfikuje hasła nawet wtedy, gdy inne kontrolery domeny w domenie nie mają zainstalowanego oprogramowania agenta kontrolera domeny. Częściowe wdrożenia tego typu nie są bezpieczne i nie są zalecane do celów testowych.

## <a name="architectural-diagram"></a>Diagram architektoniczny

Ważne jest zrozumienie podstawowych pojęć związanych z projektem i funkcjami przed wdrożeniem ochrony hasłem usługi Azure AD w środowisku lokalnym AD DS. Na poniższym diagramie przedstawiono, jak składniki ochrony hasłem usługi Azure AD współpracują ze sobą:

![Jak współpracują ze składnikami ochrony haseł usługi Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Usługa serwera proxy ochrony hasłem usługi Azure AD jest uruchamiana na wszystkich komputerach przyłączonych do domeny w bieżącym lesie AD DS. Głównym celem usługi jest przekazanie żądań pobrania zasad haseł z kontrolerów domeny do usługi Azure AD, a następnie zwrócenie odpowiedzi z usługi Azure AD do kontrolera domeny.
* Biblioteka DLL filtru haseł agenta DC otrzymuje żądania weryfikacji hasła użytkownika z systemu operacyjnego. Filtr przekazuje je do usługi agenta kontrolera domeny, która jest uruchomiona lokalnie na kontrolerze domeny.
* Usługa agenta kontrolera domeny usługi Ochrona hasłem w usłudze Azure AD otrzymuje żądania weryfikacji hasła z biblioteki DLL filtru haseł agenta DC. Usługa agenta kontrolera domeny przetwarza je przy użyciu bieżących (dostępnych lokalnie) zasad haseł i zwraca wynik *powodzenia* lub *niepowodzenia*.

## <a name="how-azure-ad-password-protection-works"></a>Jak działa ochrona hasłem w usłudze Azure AD

Lokalne składniki ochrony haseł usługi Azure AD działają w następujący sposób:

1. Każde wystąpienie usługi serwera proxy ochrony haseł usługi Azure AD anonsuje się do kontrolerów domen w lesie przez utworzenie obiektu *serviceConnectionPoint* w Active Directory.

    Każda usługa agenta DC dla ochrony hasłem usługi Azure AD również tworzy obiekt *serviceConnectionPoint* w Active Directory. Ten obiekt jest używany głównie do raportowania i diagnostyki.

1. Usługa agenta DC jest odpowiedzialna za Inicjowanie pobierania nowych zasad haseł z usługi Azure AD. Pierwszym krokiem jest odnalezienie usługi serwera proxy ochrony hasła usługi Azure AD przez przetworzenie zapytania dotyczącego lasu dla obiektów *serviceConnectionPoint* .

1. Gdy zostanie znaleziona dostępna usługa serwera proxy, Agent DC wysyła żądanie pobrania zasad haseł do usługi serwera proxy. Usługa serwera proxy z kolei wysyła żądanie do usługi Azure AD, a następnie zwraca odpowiedź na usługę agenta kontrolera domeny.

1. Gdy usługa agenta DC otrzymuje nowe zasady haseł z usługi Azure AD, usługa przechowuje zasady w dedykowanym folderze w katalogu głównym udziału folderu *SYSVOL* . Usługa agenta kontrolera domeny monitoruje również ten folder w przypadku, gdy nowsze zasady są replikowane z innych usług agenta kontrolera domeny w domenie.

1. Usługa agenta kontrolera domeny zawsze żąda nowych zasad podczas uruchamiania usługi. Po uruchomieniu usługi Agent DC sprawdza ona wiek bieżących, dostępnych lokalnie zasad, co godzinę. Jeśli zasady są starsze niż jedna godzina, Agent DC żąda nowych zasad z usługi Azure AD za pośrednictwem usługi serwera proxy zgodnie z wcześniejszym opisem. Jeśli bieżące zasady nie są starsze niż jedna godzina, Agent kontrolera domeny będzie nadal korzystać z tych zasad.

1. Gdy kontroler domeny otrzymuje zdarzenia zmiany hasła, buforowane zasady są używane do określenia, czy nowe hasło zostało zaakceptowane czy odrzucone.

### <a name="key-considerations-and-features"></a>Najważniejsze zagadnienia i funkcje

* Po pobraniu zasad haseł ochrony haseł usługi Azure AD zasady są specyficzne dla dzierżawy. Innymi słowy, zasady haseł są zawsze kombinacją listy globalnie zakazanych haseł firmy Microsoft oraz listy niestandardowych zakazanych haseł dla dzierżawy.
* Agent DC komunikuje się z usługą proxy za pośrednictwem protokołu RPC przez TCP. Usługa serwera proxy nasłuchuje tych wywołań na dynamicznym lub statycznym porcie RPC, w zależności od konfiguracji.
* Agent DC nigdy nie nasłuchuje na porcie dostępnym przez sieć.
* Usługa serwera proxy nigdy nie wywołuje usługi agenta kontrolera domeny.
* Usługa serwera proxy jest bezstanowa. Nigdy nie buforuje zasad ani żadnego innego stanu pobranego z platformy Azure.
* Usługa Agent DC zawsze korzysta z najnowszych dostępnych lokalnie zasad haseł do oszacowania hasła użytkownika. Jeśli na lokalnym kontrolerze domeny nie są dostępne zasady dotyczące haseł, hasło zostanie automatycznie zaakceptowane. Gdy tak się stanie, komunikat o zdarzeniu jest rejestrowany w celu ostrzegania administratora.
* Ochrona hasłem w usłudze Azure AD nie jest aparatem aplikacji w czasie rzeczywistym. Po zmianie konfiguracji zasad haseł w usłudze Azure AD może wystąpić opóźnienie, gdy ta zmiana osiągnie wartość i zostanie wymuszona we wszystkich kontrolerach domeny.
* Ochrona hasłem w usłudze Azure AD działa jako uzupełnienie istniejących zasad haseł AD DS, a nie do zastąpienia. Obejmuje to wszystkie inne biblioteki DLL filtru haseł innych firm, które mogą być zainstalowane. AD DS zawsze wymaga, aby wszystkie składniki walidacji hasła zgadzali się przed zaakceptowaniem hasła.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Powiązanie lasu/dzierżawy dla ochrony haseł usługi Azure AD

Wdrożenie ochrony hasłem usługi Azure AD w lesie AD DS wymaga rejestracji tego lasu w usłudze Azure AD. Wszystkie wdrożone usługi serwera proxy muszą być również zarejestrowane w usłudze Azure AD. Te rejestracje lasu i serwera proxy są skojarzone z określoną dzierżawą usługi Azure AD, która jest identyfikowana niejawnie przez poświadczenia, które są używane podczas rejestracji.

Las AD DS i wszystkie wdrożone usługi proxy w lesie muszą być zarejestrowane w ramach tej samej dzierżawy. Nie jest obsługiwane posiadanie lasu AD DS ani żadnych usług serwera proxy w tym lesie zarejestrowanych w różnych dzierżawach usługi Azure AD. Objawy takiego nieskonfigurowanego wdrożenia obejmują możliwość pobierania zasad haseł.

> [!NOTE]
> Klienci, którzy mają wiele dzierżawców usługi Azure AD, muszą więc wybrać jedną dzierżawę wyróżniającą, aby zarejestrować każdy Las do celów ochrony hasłem usługi Azure AD.

## <a name="download"></a>Pobierz

W [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)są dostępne dwa wymagane Instalatory agentów ochrony za pomocą hasła usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z lokalnej ochrony hasłem usługi Azure AD, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Wdróż lokalną ochronę hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
