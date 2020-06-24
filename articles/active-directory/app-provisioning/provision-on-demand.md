---
title: Udostępnianie użytkownikowi na żądanie przy użyciu Azure Active Directory
description: Wymuś synchronizację
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297532"
---
# <a name="on-demand-provisioning"></a>Inicjowanie obsługi na żądanie
Inicjowanie obsługi na żądanie umożliwia udostępnianie Użytkownikowi aplikacji w ciągu kilku sekund. Można użyć możliwości, aby szybko rozwiązywać problemy z konfiguracją, sprawdzać poprawność określonych wyrażeń, filtrów zakresu testów i wiele innych. 

## <a name="how-to-use-on-demand-provisioning"></a>Jak korzystać z aprowizacji na żądanie 

1. Zaloguj się do **Azure Portal**.
2. Przejdź do **aplikacji dla przedsiębiorstw**.
3. Wybierz aplikację i przejdź do strony Konfiguracja aprowizacji.
4. Skonfiguruj udostępnianie, podając poświadczenia administratora.
5. Kliknij pozycję **Zainicjuj obsługę na żądanie**.
6. Wyszukaj użytkownika według imienia, imienia, nazwiska, nazwy wyświetlanej, nazwy głównej użytkownika lub poczty e-mail.
7. Wybierz pozycję Udostępnij w dolnej części strony.

## <a name="understanding-the-provisioning-steps"></a>Opis kroków aprowizacji
Funkcja aprowizacji na żądanie próbuje wyświetlić kroki podejmowane przez usługę aprowizacji podczas aprowizacji użytkownika. Zwykle pięć kroków do aprowizacji użytkownika, a co najmniej jeden z poniższych kroków zostanie wyświetlony w środowisku udostępniania na żądanie.

### <a name="step-1-test-connection"></a>Krok 1. Test connection
Usługa aprowizacji próbuje autoryzować dostęp do aplikacji docelowej, wysyłając żądanie do użytkownika testowego. Usługa aprowizacji oczekuje odpowiedzi wskazującej, że jest autoryzowana do kontynuowania kroków aprowizacji. Ten krok jest wyświetlany tylko wtedy, gdy w kroku Wystąpił błąd. Jeśli krok zakończy się pomyślnie, nie jest wyświetlany w środowisku aprowizacji na żądanie. 

**Porady dotyczące rozwiązywania problemów**
* Upewnij się, że podano prawidłowe poświadczenia do aplikacji docelowej, takie jak token tajny i adres URL dzierżawy. Poświadczenia wymagane różnią się w zależności od aplikacji. Szczegółowe samouczki dotyczące konfiguracji można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Upewnij się, że aplikacja docelowa obsługuje filtrowanie według zgodnych atrybutów zdefiniowanych w bloku mapowania atrybutów. Może być konieczne sprawdzenie dokumentacji interfejsu API dostarczonej przez dewelopera aplikacji, aby poznać obsługiwane przez nich filtry.  
* W przypadku aplikacji Standard scim można użyć narzędzia, takiego jak program Poster, aby upewnić się, że aplikacja będzie odpowiadać na żądania autoryzacji, gdy oczekuje usługa Azure AD Provisioning. Przykładowe żądanie można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Krok 2. Importowanie użytkownika
Następnie usługa aprowizacji pobiera użytkownika z systemu źródłowego. Atrybuty użytkownika pobierane przez usługę są późniejsze do sprawdzenia, czy użytkownik znajduje się w zakresie aprowizacji, sprawdzania systemu docelowego dla istniejącego użytkownika i określania, jakie atrybuty użytkownika mają zostać wyeksportowane do systemu docelowego. 

**Wyświetl szczegóły**

Sekcja Wyświetl szczegóły zawiera właściwości użytkownika, który został zaimportowany z systemu źródłowego (np. Azure AD).

**Porady dotyczące rozwiązywania problemów**
* Importowanie użytkownika może zakończyć się niepowodzeniem, jeśli w obiekcie użytkownika w systemie źródłowym brakuje pasującego atrybutu. Możesz rozwiązać ten problem, aktualizując obiekt użytkownika o wartości pasującego atrybutu lub zmieniając pasujący atrybut w konfiguracji aprowizacji.  
* Jeśli na liście, która została zaimportowana, brakuje oczekiwanego atrybutu, upewnij się, że atrybut ma wartość w obiekcie użytkownika w systemie źródłowym. Usługa aprowizacji obecnie nie obsługuje inicjowania obsługi atrybutów o wartości null. 
* Upewnij się, że strona mapowanie atrybutu konfiguracji aprowizacji zawiera atrybut, którego oczekujesz. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Krok 3. Ustalanie, czy użytkownik jest w zasięgu
Następnie usługa aprowizacji określa, czy użytkownik należy do [zakresu](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) aprowizacji. Usługa rozważy kwestie, takie jak to, czy użytkownik jest przypisany do aplikacji, czy zakres jest ustawiony do synchronizacji przypisanej lub Synchronizuj wszystko oraz filtry zakresu zdefiniowane w konfiguracji aprowizacji.  

**Wyświetl szczegóły**

Sekcja Wyświetl szczegóły zawiera kryteria określania zakresu, które zostały ocenione. Może zostać wyświetlona jedna z następujących właściwości:
* **Aktywny w systemie źródłowym** wskazuje, że użytkownik ma właściwość aktywna ustawiona na wartość true w usłudze Azure AD.
* **Przypisane do aplikacji** wskazuje, że użytkownik jest przypisany do aplikacji w usłudze Azure AD
* Opcja **Synchronizacja zakresu** wskazuje, że ustawienie zakresu zezwala na wszystkich użytkowników i grupy w dzierżawie.
* **Użytkownik ma wymaganą rolę** wskazuje, że użytkownik ma wymagane role do zainicjowania obsługi w aplikacji. 
* **Filtry zakresu** są również wyświetlane, jeśli zdefiniowano filtry określania zakresu dla aplikacji. Filtr będzie wyświetlany w następującym formacie: {tytuł filtru określania zakresu} {atrybut filtru zakresu} {Określanie zakresu filtru} {wartość filtru zakresu}. 

**Porady dotyczące rozwiązywania problemów**
* Upewnij się, że zdefiniowano prawidłową rolę określania zakresu. Na przykład Unikaj używania operatora ["większe niż"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) z wartością niecałkowitą. 
* Jeśli użytkownik nie ma wymaganej roli, zapoznaj się ze wskazówkami opisanymi [tutaj](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role). 

### <a name="step-4-match-user-between-source-and-target"></a>Krok 4. dopasowanie użytkownika do źródła i celu
W tym kroku. usługa próbuje dopasować użytkownika, który został pobrany w kroku importowania z użytkownikiem w systemie docelowym. 

**Wyświetl szczegóły**

Na stronach Wyświetl szczegóły są wyświetlane właściwości użytkowników, które zostały dopasowane w systemie docelowym. Właściwości wyświetlane w okienku kontekstowym będą się różnić w następujący sposób:
* Jeśli w systemie docelowym nie ma pasujących użytkowników, nie zobaczysz żadnych właściwości.
* Jeśli w systemie docelowym jest zgodny jeden użytkownik, zostaną wyświetlone właściwości tego dopasowanego użytkownika z systemu docelowego.
* W przypadku dopasowania wielu użytkowników zostaną wyświetlone właściwości obu pasujących użytkowników.
* Jeśli wiele pasujących atrybutów jest częścią mapowań atrybutów, każdy pasujący atrybut będzie oceniany sekwencyjnie i wyświetlane dopasowane użytkownicy. 

**Szczegóły rozwiązywania problemów**
* Usługa aprowizacji nie może jednoznacznie dopasować użytkownika w źródle do użytkownika w miejscu docelowym. Można rozwiązać ten problem, upewniając się, że pasujący atrybut jest unikatowy. 
* Upewnij się, że aplikacja docelowa obsługuje filtrowanie na atrybucie zdefiniowanym jako pasujący atrybut.  

### <a name="step-5-perform-action"></a>Krok 5. wykonywanie akcji
Na koniec usługa aprowizacji wykonuje akcję, taką jak tworzenie, aktualizowanie, usuwanie lub pomijanie użytkownika. 

**Wyświetl szczegóły**

Sekcja Wyświetl szczegóły zawiera atrybuty, które zostały zmodyfikowane w aplikacji docelowej. Reprezentuje końcowe dane wyjściowe działania usługi aprowizacji i wyeksportowane atrybuty. Jeśli ten krok zakończy się niepowodzeniem, wyświetlane atrybuty reprezentują atrybuty, które usługa aprowizacji próbowała zmodyfikować.  

**Porady dotyczące rozwiązywania problemów**
* Błędy eksportowania zmian mogą się znacznie różnić. Zapoznaj się z [dokumentacją dotyczącą](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) dzienników aprowizacji, aby zapoznać się z typowymi błędami.


## <a name="frequently-asked-questions"></a>Często zadawane pytania
**Czy konieczne jest włączenie aprowizacji, aby można było korzystać z aprowizacji na żądanie?** W przypadku aplikacji, które używają długiego tokenu lub nazwy użytkownika i hasła do autoryzacji, nie są wymagane żadne dodatkowe czynności. Aplikacje korzystające z protokołu OAuth do autoryzacji obecnie wymagają zatrzymania zadania aprowizacji przed rozpoczęciem korzystania z aprowizacji na żądanie. Aplikacje, takie jak G Suite, Box, miejsce pracy w serwisie Facebook i zapasy czasu, należą do tej kategorii. Pracuj w toku, aby umożliwić uruchamianie aprowizacji na żądanie dla wszystkich aplikacji bez konieczności zatrzymywania aprowizacji. 

**Jak długo trwa inicjowanie obsługi na żądanie?** Zwykle trwa mniej niż 30 sekund. 

## <a name="known-limitations"></a>Znane ograniczenia
Dzisiaj istnieje kilka znanych ograniczeń. Po opublikowaniu w usłudze [UserVoice](https://aka.ms/appprovisioningfeaturerequest) będziemy mogli lepiej określić priorytety ulepszeń, które należy wprowadzić dalej. Należy pamiętać, że te ograniczenia są specyficzne dla możliwości aprowizacji na żądanie. Aby uzyskać szczegółowe informacje o tym, czy aplikacja obsługuje grupy aprowizacji, usunięcia itp., należy zapoznać się z samouczkiem dotyczącym aplikacji. 

* Aplikacje Workday, AWS i SuccessFactors nie obsługują aprowizacji na żądanie.
* Inicjowanie obsługi administracyjnej grup i ról na żądanie nie jest obsługiwane.
* Wyłączenie lub usunięcie użytkowników i grup nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z obsługą administracyjną](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
