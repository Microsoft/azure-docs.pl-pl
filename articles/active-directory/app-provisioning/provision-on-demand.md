---
title: Udostępnianie użytkownikowi na żądanie przy użyciu Azure Active Directory
description: Wymuś synchronizację
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255665"
---
# <a name="on-demand-provisioning"></a>Aprowizacja na żądanie
Użyj aprowizacji na żądanie, aby zapewnić użytkownikowi dostęp do aplikacji w ciągu kilku sekund. Można między innymi skorzystać z tej możliwości, aby:

* Szybkie rozwiązywanie problemów z konfiguracją.
* Sprawdź poprawność zdefiniowanych wyrażeń.
* Filtry zakresu testów.

## <a name="how-to-use-on-demand-provisioning"></a>Jak korzystać z aprowizacji na żądanie

1. Zaloguj się w witrynie **Azure Portal**.
1. Przejdź do **wszystkich usług**  >  **aplikacje dla przedsiębiorstw**.
1. Wybierz aplikację, a następnie przejdź do strony Konfiguracja aprowizacji.
1. Skonfiguruj udostępnianie, podając poświadczenia administratora.
1. Wybierz pozycję **Udostępnij na żądanie**.
1. Wyszukaj użytkownika według imienia, imienia, nazwiska, nazwy wyświetlanej, nazwy głównej użytkownika lub adresu e-mail.
   > [!NOTE]
   > W przypadku aplikacji do aprowizacji w chmurze (Workday/SuccessFactors do usługi AD/Azure AD) wartość wejściowa jest różna. W przypadku scenariusza Workday Podaj "WID" użytkownika w dniach roboczych. W przypadku scenariusza SuccessFactors podaj wartość "personIdExternal" użytkownika w SuccessFactors. 
 
1. Wybierz pozycję **Udostępnij** w dolnej części strony.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Zrzut ekranu przedstawiający interfejs użytkownika Azure Portal na potrzeby aprowizacji użytkownika na żądanie.":::

## <a name="understand-the-provisioning-steps"></a>Opis kroków aprowizacji

Proces aprowizacji na żądanie próbuje wyświetlić kroki, które usługa aprowizacji podejmuje podczas aprowizacji użytkownika. Zwykle pięć kroków, aby zainicjować obsługę administracyjną użytkownika. Co najmniej jeden z tych kroków wyjaśniono w poniższych sekcjach, zostanie wyświetlony w trakcie udostępniania na żądanie.

### <a name="step-1-test-connection"></a>Krok 1. Test connection

Usługa aprowizacji próbuje autoryzować dostęp do aplikacji docelowej, wysyłając żądanie do użytkownika testowego. Usługa aprowizacji oczekuje odpowiedzi wskazującej, że usługa jest uprawniona do kontynuowania kroków aprowizacji. Ten krok jest wyświetlany tylko wtedy, gdy nie powiedzie się. Nie jest on wyświetlany w trakcie inicjowania obsługi na żądanie, gdy krok zakończy się pomyślnie.

#### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Upewnij się, że podano prawidłowe poświadczenia, takie jak token tajny i adres URL dzierżawy, do aplikacji docelowej. Wymagane poświadczenia różnią się w zależności od aplikacji. Aby uzyskać szczegółowe samouczki dotyczące konfiguracji, zobacz [listę samouczków](../saas-apps/tutorial-list.md). 
* Upewnij się, że aplikacja docelowa obsługuje filtrowanie zgodnie z pasującymi atrybutami zdefiniowanymi w okienku **mapowania atrybutów** . Może być konieczne sprawdzenie dokumentacji interfejsu API dostarczonej przez dewelopera aplikacji, aby poznać obsługiwane filtry.
* W przypadku aplikacji do zarządzania tożsamościami między domenami (standard scim) można użyć narzędzia, takiego jak Poster. Takie narzędzia ułatwiają zapewnienie, że aplikacja będzie odpowiadać na żądania autoryzacji w sposób, w jaki oczekuje usługa aprowizacji Azure Active Directory (Azure AD). Zapoznaj się z [przykładowym żądaniem](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Krok 2. Importowanie użytkownika

Następnie usługa aprowizacji pobiera użytkownika z systemu źródłowego. Atrybuty użytkownika pobierane przez usługę są używane w dalszej części:

* Oceń, czy użytkownik należy do zakresu aprowizacji.
* Sprawdź system docelowy dla istniejącego użytkownika.
* Ustal, jakie atrybuty użytkownika mają zostać wyeksportowane do systemu docelowego.

#### <a name="view-details"></a>Wyświetl szczegóły


Sekcja **Wyświetl szczegóły** zawiera właściwości użytkownika, który został zaimportowany z systemu źródłowego (na przykład Azure AD).

#### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Importowanie użytkownika może zakończyć się niepowodzeniem, jeśli w obiekcie użytkownika w systemie źródłowym brakuje pasującego atrybutu. Aby rozwiązać ten problem, wypróbuj jedną z następujących metod:

  * Zaktualizuj obiekt użytkownika przy użyciu wartości pasującego atrybutu.
  * Zmień pasujący atrybut w konfiguracji aprowizacji.

* Jeśli brakuje oczekiwanego atrybutu na liście importowanych, upewnij się, że atrybut ma wartość w obiekcie użytkownika w systemie źródłowym. Usługa aprowizacji obecnie nie obsługuje inicjowania obsługi atrybutów o wartości null.
* Upewnij się, że strona **Mapowanie atrybutu** w konfiguracji aprowizacji zawiera oczekiwany atrybut.

### <a name="step-3-determine-if-user-is-in-scope"></a>Krok 3. Ustalanie, czy użytkownik jest w zasięgu

Następnie usługa aprowizacji określa, czy użytkownik należy do [zakresu](./how-provisioning-works.md#scoping) aprowizacji. Usługa rozważa kwestie takie jak:

* Określa, czy użytkownik jest przypisany do aplikacji.
* Określa, czy zakres jest ustawiony na wartość **Synchronizuj przypisane** lub **zsynchronizuj wszystko**.
* Filtry zakresu zdefiniowane w konfiguracji aprowizacji.  

#### <a name="view-details"></a>Wyświetl szczegóły

Sekcja **Wyświetl szczegóły** zawiera kryteria określania zakresu, które zostały ocenione. Może zostać wyświetlona co najmniej jedna z następujących właściwości:

* **Aktywny w systemie źródłowym** wskazuje, że użytkownik ma właściwość `IsActive` ustawioną na **wartość true** w usłudze Azure AD.
* **Przypisane do aplikacji** wskazuje, że użytkownik jest przypisany do aplikacji w usłudze Azure AD.
* Opcja **Synchronizacja zakresu** wskazuje, że ustawienie zakresu zezwala na wszystkich użytkowników i grupy w dzierżawie.
* **Użytkownik ma wymaganą rolę** wskazuje, że użytkownik ma wymagane role do zainicjowania obsługi w aplikacji. 
* **Filtry zakresu** są również wyświetlane w przypadku zdefiniowania filtrów określania zakresu dla aplikacji. Filtr jest wyświetlany w następującym formacie: {tytuł filtru określania zakresu} {atrybut filtru określania zakresu} {zakres filtru zakresu} {wartość filtru zakresu}.

#### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Upewnij się, że zdefiniowano prawidłową rolę określania zakresu. Na przykład Unikaj używania [operatora Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) z wartością niecałkowitą.
* Jeśli użytkownik nie ma wymaganej roli, zapoznaj się ze [wskazówkami dotyczącymi aprowizacji użytkowników przypisanych do domyślnej roli dostępu](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Krok 4. dopasowanie użytkownika do źródła i celu

W tym kroku usługa próbuje dopasować użytkownika, który został pobrany w kroku importowania z użytkownikiem w systemie docelowym.

#### <a name="view-details"></a>Wyświetl szczegóły

Na stronie **Wyświetl szczegóły** są wyświetlane właściwości użytkowników, którzy zostali zgodni w systemie docelowym. Właściwości, które są wyświetlane w okienku kontekstu, różnią się w następujący sposób:

* Jeśli żaden użytkownik nie jest zgodny z systemem docelowym, nie zobaczysz żadnych właściwości.
* Jeśli w systemie docelowym jest zgodny jeden użytkownik, zobaczysz właściwości tego dopasowanego użytkownika z systemu docelowego.
* W przypadku dopasowania wielu użytkowników zostaną wyświetlone właściwości obu pasujących użytkowników.
* Jeśli wiele pasujących atrybutów jest częścią mapowań atrybutów, każdy pasujący atrybut jest obliczany sekwencyjnie i są wyświetlane dopasowane użytkownicy dla tego atrybutu.

#### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Usługa aprowizacji może nie być w stanie dopasować użytkownika w systemie źródłowym do unikatowego użytkownika w miejscu docelowym. Rozwiąż ten problem, upewniając się, że pasujący atrybut jest unikatowy.
* Upewnij się, że aplikacja docelowa obsługuje filtrowanie na atrybucie zdefiniowanym jako pasujący atrybut.  

### <a name="step-5-perform-action"></a>Krok 5. wykonywanie akcji

Na koniec usługa aprowizacji wykonuje akcję, taką jak tworzenie, aktualizowanie, usuwanie lub pomijanie użytkownika.

Oto przykład tego, co może być widoczne po pomyślnym zainicjowaniu obsługi użytkownika na żądanie:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Zrzut ekranu pokazujący Pomyślne inicjowanie obsługi użytkownika.":::

#### <a name="view-details"></a>Wyświetl szczegóły

Sekcja **Wyświetl szczegóły** zawiera atrybuty, które zostały zmodyfikowane w aplikacji docelowej. Ten widok przedstawia końcowe dane wyjściowe działania usługi aprowizacji oraz wyeksportowane atrybuty. Jeśli ten krok zakończy się niepowodzeniem, wyświetlane atrybuty reprezentują atrybuty, które usługa aprowizacji próbowała zmodyfikować.

#### <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Błędy eksportowania zmian mogą się znacznie różnić. Zapoznaj się z [dokumentacją dla dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md#error-codes) , aby uzyskać typowe błędy.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy konieczne jest włączenie aprowizacji, aby można było korzystać z aprowizacji na żądanie?** W przypadku aplikacji, które używają długiego tokenu okaziciela lub nazwy użytkownika i hasła do autoryzacji, nie są wymagane żadne dodatkowe czynności. Aplikacje korzystające z protokołu OAuth do autoryzacji obecnie wymagają zatrzymania zadania aprowizacji przed rozpoczęciem korzystania z aprowizacji na żądanie. Aplikacje, takie jak G Suite, Box, miejsce pracy w serwisie Facebook i zapasy czasu, należą do tej kategorii. Praca jest w toku do obsługi aprowizacji na żądanie dla wszystkich aplikacji bez konieczności zatrzymywania zadań aprowizacji.

* **Jak długo trwa inicjowanie obsługi na żądanie?** Inicjowanie obsługi na żądanie trwa zwykle krócej niż 30 sekund.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie istnieje kilka znanych ograniczeń dotyczących aprowizacji na żądanie. Opublikuj swoje [sugestie i opinie](https://aka.ms/appprovisioningfeaturerequest) , aby lepiej określić, jakie ulepszenia należy wprowadzić dalej.

> [!NOTE]
> Następujące ograniczenia są specyficzne dla możliwości aprowizacji na żądanie. Aby uzyskać informacje o tym, czy aplikacja obsługuje grupy aprowizacji, usunięcia lub inne możliwości, należy zapoznać się z samouczkiem dotyczącym tej aplikacji.

* Aplikacja Amazon Web Services (AWS) nie obsługuje aprowizacji na żądanie. 
* Obsługa administracyjna grup i ról na żądanie nie jest obsługiwana.
* Inicjowanie obsługi na żądanie obsługuje wyłączanie użytkowników, którzy zostali Nieprzypisani z aplikacji. Nie obsługuje jednak wyłączania ani usuwania użytkowników, które zostały wyłączone lub usunięte z usługi Azure AD. Ci użytkownicy nie będą wyświetlani podczas wyszukiwania użytkownika.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z obsługą administracyjną](./application-provisioning-config-problem.md)