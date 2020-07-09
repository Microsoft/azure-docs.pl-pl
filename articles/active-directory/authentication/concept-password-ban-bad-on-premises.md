---
title: Ochrona hasłem w usłudze Azure AD — Azure Active Directory
description: Zablokuj słabe hasła w Active Directory lokalnym przy użyciu funkcji ochrony hasłem w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848650"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Enforce Azure AD password protection for Windows Server Active Directory (Wymuszanie ochrony hasłem usługi Azure AD dla usługi Active Directory systemu Windows Server)

Ochrona hasłem w usłudze Azure AD to funkcja, która podwyższa poziom zasad haseł w organizacji. Lokalne wdrożenie ochrony hasłem korzysta z globalnej i niestandardowej listy zakazanych haseł, które są przechowywane w usłudze Azure AD. To samo sprawdzenie, czy usługa Azure AD jest w przypadku zmian opartych na chmurze. Te testy są wykonywane podczas zmiany hasła i scenariuszy resetowania hasła.

## <a name="design-principles"></a>Zasady projektowania

Ochrona hasłem usługi Azure AD jest zaprojektowana z uwzględnieniem następujących zasad:

* Kontrolery domeny nigdy nie muszą komunikować się bezpośrednio z Internetem.
* Na kontrolerach domeny nie są otwierane żadne nowe porty sieciowe.
* Nie są wymagane żadne zmiany schematu Active Directory. Oprogramowanie używa istniejącego **kontenera** Active Directory i obiektów schematu **serviceConnectionPoint** .
* Nie jest wymagany minimalny Active Directory domeny ani poziomu funkcjonalności lasu (DFL/FFL).
* Oprogramowanie nie tworzy ani nie wymaga kont w domenach Active Directory, które chroni.
* Hasła w postaci zwykłego tekstu użytkownika nigdy nie opuszczają kontrolera domeny, podczas operacji weryfikacji hasła lub w innym czasie.
* Oprogramowanie nie jest zależne od innych funkcji usługi Azure AD; na przykład synchronizacja skrótów haseł w usłudze Azure AD nie jest powiązana i nie jest wymagana w celu zapewnienia funkcji ochrony hasłem usługi Azure AD.
* Wdrożenie przyrostowe jest obsługiwane, ale zasady haseł są wymuszane tylko wtedy, gdy jest zainstalowany agent kontrolera domeny (Agent DC). Aby uzyskać więcej informacji, zobacz następny temat.

## <a name="incremental-deployment"></a>Wdrożenie przyrostowe

Ochrona hasłem w usłudze Azure AD obsługuje wdrożenie przyrostowe między kontrolerami domeny w domenie Active Directory, ale ważne jest, aby zrozumieć, co to naprawdę znaczy i jakie są kompromisy.

Oprogramowanie agenta DC ochrony hasłem w usłudze Azure AD może weryfikować tylko hasła, gdy jest ono zainstalowane na kontrolerze domeny i tylko dla zmian haseł wysyłanych do tego kontrolera domeny. Nie jest możliwe sterowanie kontrolerami domeny wybieranymi przez komputery klienckie z systemem Windows w celu przetwarzania zmian hasła użytkownika. W celu zagwarantowania spójnych zachowań i wymuszania bezpieczeństwa uniwersalnego hasła należy zainstalować oprogramowanie agenta kontrolera domeny na wszystkich kontrolerach domeny w domenie.

Wiele organizacji chce przeprowadzić dokładne testowanie ochrony hasłem usługi Azure AD na podzestawie kontrolerów domeny przed wdrożeniem pełnego wdrożenia. Ochrona hasłem w usłudze Azure AD obsługuje wdrażanie częściowe, a program IE oprogramowanie agenta kontrolera domeny na danym kontrolerze domeny będzie aktywnie weryfikować hasła, nawet jeśli w przypadku innych kontrolerów domeny w domenie nie zainstalowano oprogramowania agenta kontrolera domeny. Częściowe wdrożenia tego typu nie są bezpieczne i nie są zalecane w celach testowych.

## <a name="architectural-diagram"></a>Diagram architektoniczny

Ważne jest zrozumienie podstawowych pojęć związanych z projektem i funkcjami przed wdrożeniem ochrony hasłem usługi Azure AD w środowisku lokalnym Active Directory. Na poniższym diagramie przedstawiono, jak składniki ochrony hasłem współpracują ze sobą:

![Jak współpracują ze składnikami ochrony haseł usługi Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Usługa serwera proxy ochrony hasłem usługi Azure AD jest uruchamiana na wszystkich komputerach przyłączonych do domeny w bieżącym lesie Active Directory. Podstawowym celem jest przekazanie żądań pobrania zasad haseł z kontrolerów domeny do usługi Azure AD. Następnie zwraca odpowiedzi z usługi Azure AD do kontrolera domeny.
* Biblioteka DLL filtru haseł agenta DC otrzymuje żądania weryfikacji hasła użytkownika z systemu operacyjnego. Przekazuje je do usługi agenta DC, która jest uruchomiona lokalnie na kontrolerze domeny.
* Usługa agenta kontrolera domeny ochrony hasłem otrzymuje żądania weryfikacji hasła z biblioteki DLL filtru haseł agenta kontrolera domeny. Przetwarza je przy użyciu bieżących (lokalnie dostępnych) zasad haseł i zwraca wynik: *Pass* lub *Niepowodzenie*.

## <a name="how-password-protection-works"></a>Jak działa ochrona hasłem

Każde wystąpienie usługi serwera proxy ochrony haseł usługi Azure AD anonsuje się do kontrolerów domeny w lesie, tworząc obiekt **serviceConnectionPoint** w Active Directory.

Każda usługa agenta DC do ochrony hasłem tworzy również obiekt **serviceConnectionPoint** w Active Directory. Ten obiekt jest używany głównie do raportowania i diagnostyki.

Usługa agenta DC jest odpowiedzialna za Inicjowanie pobierania nowych zasad haseł z usługi Azure AD. Pierwszym krokiem jest odnalezienie usługi serwera proxy ochrony hasła usługi Azure AD przez przetworzenie zapytania dotyczącego lasu dla obiektów **serviceConnectionPoint** . Gdy zostanie znaleziona dostępna usługa serwera proxy, Agent DC wysyła żądanie pobrania zasad haseł do usługi serwera proxy. Usługa serwera proxy z kolei wysyła żądanie do usługi Azure AD. Usługa serwera proxy zwraca odpowiedź do usługi agenta kontrolera domeny.

Gdy usługa agenta DC otrzymuje nowe zasady haseł z usługi Azure AD, usługa przechowuje zasady w dedykowanym folderze w katalogu głównym udziału folderu *SYSVOL* . Usługa agenta kontrolera domeny monitoruje również ten folder w przypadku, gdy nowsze zasady są replikowane z innych usług agenta kontrolera domeny w domenie.

Usługa agenta kontrolera domeny zawsze żąda nowych zasad podczas uruchamiania usługi. Po uruchomieniu usługi Agent DC sprawdza ona wiek bieżących, dostępnych lokalnie zasad, co godzinę. Jeśli zasady są starsze niż jedna godzina, Agent DC żąda nowych zasad z usługi Azure AD za pośrednictwem usługi serwera proxy zgodnie z wcześniejszym opisem. Jeśli bieżące zasady nie są starsze niż jedna godzina, Agent kontrolera domeny będzie nadal korzystać z tych zasad.

Po pobraniu zasad haseł ochrony haseł usługi Azure AD zasady są specyficzne dla dzierżawy. Innymi słowy, zasady haseł są zawsze kombinacją listy globalnie zakazanych haseł firmy Microsoft oraz listy niestandardowych zakazanych haseł dla dzierżawy.

Agent DC komunikuje się z usługą proxy za pośrednictwem protokołu RPC przez TCP. Usługa serwera proxy nasłuchuje tych wywołań na dynamicznym lub statycznym porcie RPC, w zależności od konfiguracji.

Agent DC nigdy nie nasłuchuje na porcie dostępnym przez sieć.

Usługa serwera proxy nigdy nie wywołuje usługi agenta kontrolera domeny.

Usługa serwera proxy jest bezstanowa. Nigdy nie buforuje zasad ani żadnego innego stanu pobranego z platformy Azure.

Usługa Agent DC zawsze korzysta z najnowszych dostępnych lokalnie zasad haseł do oszacowania hasła użytkownika. Jeśli na lokalnym kontrolerze domeny nie są dostępne zasady dotyczące haseł, hasło zostanie automatycznie zaakceptowane. Gdy tak się stanie, komunikat o zdarzeniu jest rejestrowany w celu ostrzegania administratora.

Ochrona hasłem w usłudze Azure AD nie jest aparatem aplikacji w czasie rzeczywistym. Po zmianie konfiguracji zasad haseł w usłudze Azure AD może wystąpić opóźnienie, gdy ta zmiana osiągnie wartość i zostanie wymuszona na wszystkich kontrolerach domeny.

Ochrona hasłem w usłudze Azure AD działa jako uzupełnienie istniejących zasad haseł Active Directory, a nie do zastąpienia. Obejmuje to wszystkie inne biblioteki DLL filtru haseł innych firm, które mogą być zainstalowane. Active Directory zawsze wymaga, aby wszystkie składniki walidacji hasła zgadzali się przed zaakceptowaniem hasła.

## <a name="foresttenant-binding-for-password-protection"></a>Powiązanie lasu/dzierżawy do ochrony haseł

Wdrożenie ochrony hasłem usługi Azure AD w lesie Active Directory wymaga rejestracji tego lasu w usłudze Azure AD. Wszystkie wdrożone usługi serwera proxy muszą być również zarejestrowane w usłudze Azure AD. Te rejestracje lasu i serwera proxy są skojarzone z określoną dzierżawą usługi Azure AD, która jest identyfikowana niejawnie przez poświadczenia, które są używane podczas rejestracji.

Las Active Directory i wszystkie wdrożone usługi proxy w lesie muszą być zarejestrowane w ramach tej samej dzierżawy. Nie jest obsługiwane posiadanie lasu Active Directory ani żadnych usług serwera proxy w tym lesie zarejestrowanych w różnych dzierżawach usługi Azure AD. Objawy takiego nieskonfigurowanego wdrożenia obejmują możliwość pobierania zasad haseł.

## <a name="download"></a>Pobierz

W [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)są dostępne dwa wymagane Instalatory agentów ochrony za pomocą hasła usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
