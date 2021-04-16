---
title: Jak skonfigurować samoobsługowe przypisywanie aplikacji | Microsoft Docs
description: Włącz dostęp do aplikacji samoobsługowej, aby umożliwić użytkownikom znajdowanie własnych aplikacji
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373965"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Jak skonfigurować samoobsługowe przypisywanie aplikacji

Zanim użytkownicy będą w stanie samodzielnie odnajdywać aplikacje  z ich Moje aplikacje, należy włączyć dostęp do aplikacji samoobsługi do wszystkich aplikacji, do których chcesz zezwolić użytkownikom na samodzielne odnajdywanie i żądanie dostępu. Ta funkcja jest dostępna dla aplikacji, które zostały dodane z galerii usługi [Azure AD,](./add-application-portal.md) [usługi Azure AD serwer proxy aplikacji](./application-proxy.md) lub dodane za pośrednictwem zgody użytkownika lub [administratora.](../develop/application-consent-experience.md) 

Ta funkcja to doskonały sposób, aby zaoszczędzić czas i pieniądze jako grupa IT. Jest ona zdecydowanie zalecana w ramach nowoczesnego wdrażania aplikacji z Azure Active Directory.

Przy użyciu tej funkcji można:

-   Pozwól użytkownikom samodzielnie odnajdywać aplikacje z Moje aplikacje [bez](https://myapps.microsoft.com/) przeszkadzania grupie IT.

-   Dodaj tych użytkowników do wstępnie skonfigurowanej grupy, aby zobaczyć, kto zażądał dostępu, usunąć dostęp i zarządzać przypisanymi do nich rolami.

-   Opcjonalnie osoba zatwierdzająca w firmie może zatwierdzać żądania dostępu do aplikacji, dzięki czemu grupa IT nie musi tego wymagać.

-   Opcjonalnie skonfiguruj maksymalnie 10 osób, które mogą zatwierdzić dostęp do tej aplikacji.

-   Opcjonalnie możesz zezwolić osobom zatwierdzającym na ustawianie haseł, których użytkownicy mogą używać do logowania się do aplikacji, bezpośrednio z aplikacji osoby zatwierdzającej [Moje aplikacje.](https://myapps.microsoft.com/)

-   Opcjonalnie możesz automatycznie przypisywać użytkowników przypisanych do samoobsługi bezpośrednio do roli aplikacji.

> [!NOTE]
> Licencja Azure Active Directory — wersja Premium (P1 lub P2) jest wymagana, aby użytkownicy prosili o dołączenie do aplikacji samoobsługowej oraz aby właściciele zatwierdzali lub odrzucali żądania. Bez licencji Azure Active Directory — wersja Premium użytkownicy nie mogą dodawać aplikacji samoobsługowych.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Włącz dostęp do aplikacji samoobsługowej, aby umożliwić użytkownikom znajdowanie własnych aplikacji

Samoobsługowy dostęp do aplikacji to doskonały sposób, aby umożliwić użytkownikom samodzielne odnajdywanie aplikacji i opcjonalnie zezwolić grupie biznesowej na zatwierdzenie dostępu do tych aplikacji. W przypadku aplikacji z logowaniem pojedynczym hasłem można również zezwolić grupie biznesowej na zarządzanie poświadczeniami przypisanymi do tych użytkowników z własnych paneli Moje aplikacje dostępu.

Aby włączyć dostęp aplikacji samoobsługowej do aplikacji, wykonaj poniższe kroki:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) jako administrator globalny.

2. Wybierz pozycję **Azure Active Directory**. W menu nawigacji po lewej stronie wybierz pozycję **Aplikacje dla przedsiębiorstw.**

3. Wybierz aplikację z listy. Jeśli nie widzisz aplikacji, zacznij wpisywać jej nazwę w polu wyszukiwania. Możesz też użyć kontrolek filtru, aby wybrać typ, stan lub widoczność aplikacji, a następnie wybrać pozycję **Zastosuj.**

4. W menu nawigacji po lewej stronie wybierz **pozycję Samoobsługa.**

5. Aby włączyć dostęp do aplikacji samoobsługowej dla tej aplikacji, przełącz przełącznik Zezwalaj użytkownikom na żądanie dostępu do tej **aplikacji?** na **pozycję Tak.**

6. Obok opcji **Do której grupy należy dodać przypisanych użytkowników?** kliknij pozycję Wybierz **grupę.** Wybierz grupę, a następnie kliknij pozycję **Wybierz.** Po zatwierdzeniu żądania użytkownika zostanie on dodany do tej grupy. Podczas wyświetlania członkostwa w tej grupie będzie można zobaczyć, kto otrzymał dostęp do aplikacji za pośrednictwem dostępu samoobsługowego.
  
    > [!NOTE]
    > To ustawienie nie obsługuje grup synchronizowanych ze środowisk lokalnych.

7. **Opcjonalnie:** Aby wymagać zatwierdzenia firmy przed zezwoleniem użytkownikom na dostęp, ustaw przełącznik Wymagaj zatwierdzenia przed udzieleniem dostępu **do tej aplikacji?** na **wartość Tak.**

8. **Opcjonalnie:** w przypadku aplikacji korzystających tylko z logowania pojedynczego hasła, aby umożliwić użytkownikom zatwierdzającym w firmie określenie haseł wysyłanych do tej aplikacji dla zatwierdzonych użytkowników, ustaw przełącznik Zezwalaj osobam zatwierdzającym na ustawianie haseł użytkowników dla tej **aplikacji?** na wartość **Tak.**

9. **Opcjonalnie:** Aby określić osoby zatwierdzające w firmie, które mogą zatwierdzać dostęp do tej aplikacji, obok opcji Kto może zatwierdzić dostęp do tej **aplikacji?** kliknij pozycję Wybierz osoby **zatwierdzające,** a następnie wybierz maksymalnie 10 osób zatwierdzających w firmie. Następnie kliknij pozycję **Wybierz**.

    >[!NOTE]
    >Grupy nie są obsługiwane. Możesz wybrać maksymalnie 10 osób zatwierdzających w firmie. W przypadku określenia wielu osób zatwierdzających każda osoba zatwierdzająca może zatwierdzić żądanie dostępu.

10. **Opcjonalnie:** w przypadku aplikacji, które uwidoczniają role **,** aby przypisać użytkowników zatwierdzonych do samoobsługi do roli, obok roli Do której roli powinni być przypisani użytkownicy w tej **aplikacji?** kliknij pozycję Wybierz **rolę,** a następnie wybierz rolę, do której ci użytkownicy powinni zostać przypisani. Następnie kliknij pozycję **Wybierz**.

11. Kliknij przycisk **Zapisz** w górnej części okienka, aby zakończyć.

Po zakończeniu konfiguracji aplikacji samoobsługowej użytkownicy mogą przejść do swoich  aplikacji [Moje aplikacje](https://myapps.microsoft.com/) i kliknąć przycisk Dodaj aplikacje samoobsługowe, aby znaleźć aplikacje, które są włączane z dostępem samoobsługi. Osoby zatwierdzające w firmie widzą również powiadomienie w [Moje aplikacje](https://myapps.microsoft.com/). Możesz włączyć wiadomość e-mail z powiadomieniem, gdy użytkownik zażąda dostępu do aplikacji, która wymaga zatwierdzenia.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie usługi Azure Active Directory do samoobsługowego zarządzania grupami](../enterprise-users/groups-self-service-management.md)