---
title: Rozwiązywanie typowych błędów — Azure Database for MySQL
description: Dowiedz się, jak rozwiązywać typowe błędy migracji napotykane przez użytkowników nowych do usługi Azure Database for MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ca75416a66bcf2c90028c7f1dc11fbe23a9a9bd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631371"
---
# <a name="common-errors"></a>Typowe błędy

Azure Database for MySQL to w pełni zarządzana usługa oparta na wersji społeczności MySQL. Środowisko MySQL w środowisku usługi zarządzanej może się różnić od uruchamiania programu MySQL w Twoim środowisku. W tym artykule zobaczysz niektóre typowe błędy, które użytkownicy mogą napotkać podczas migrowania lub opracowywania usługi Azure Database for MySQL po raz pierwszy.

## <a name="common-connection-errors"></a>Typowe błędy połączeń

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>BŁĄD 1184 (08S01): przerwano połączenie z 22 do DB: "DB-Name" User: "User" host: "hostIP" (init_connect polecenie nie powiodło się)
Powyższy błąd występuje po pomyślnym zalogowaniu, ale przed wykonaniem dowolnego polecenia po ustanowieniu sesji. Powyższy komunikat oznacza, że ustawiono niepoprawną wartość parametru serwera init_connect, co powoduje niepowodzenie inicjowania sesji.

Istnieją pewne parametry serwera, takie jak require_secure_transport, które nie są obsługiwane na poziomie sesji i dlatego próba zmiany wartości tych parametrów przy użyciu init_connect może spowodować błąd 1184 podczas łączenia się z serwerem MySQL, jak pokazano poniżej

Baza danych MySQL> show Database; BŁĄD 2006 (HY000): serwer MySQL nie ma połączenia. Trwa próba ponownego nawiązania połączenia... Identyfikator połączenia: 64897 bieżąca baza danych: * * * brak * * * błąd 1184 (08S01): przerwane połączenie 22 do bazy danych: "DB-Name" User: "User" host: "hostIP" (polecenie init_connect nie powiodło się)

**Rozwiązanie** : należy zresetować wartość init_connect na karcie parametry serwera w Azure Portal i ustawić tylko obsługiwane parametry serwera przy użyciu parametru init_connect. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Błędy ze względu na brak uprawnienia administratora i roli administratora systemu

Rola noprivilege i DBA nie są obsługiwane w usłudze. W związku z tym mogą wystąpić następujące typowe błędy wymienione poniżej:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>BŁĄD 1419: nie masz włączonego uprawnienia i rejestrowanie danych binarnych ( *może* być konieczne użycie mniej bezpiecznej log_bin_trust_function_creators zmiennej).

Powyższy błąd może wystąpić podczas tworzenia funkcji, wyzwalacza jak poniżej lub importowania schematu. Instrukcje języka DDL, takie jak CREATE FUNCTION lub CREATE TRIGGER, są zapisywane w dzienniku binarnym, więc replika pomocnicza może je uruchomić. Wątek SQL repliki ma pełne uprawnienia, które można wykorzystać w celu podniesienia uprawnień. Aby zapewnić ochronę przed tym zagrożeniem w przypadku serwerów z włączonym logowaniem binarnym, aparat MySQL wymaga, aby w przypadku wszystkich wymaganych przywilejów tworzenia musi mieć uprawnienia administratora. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Rozwiązanie**: Aby rozwiązać ten problem, ustaw log_bin_trust_function_creators na 1 z bloku [parametrów serwera](howto-server-parameters.md) w portalu, wykonaj instrukcje języka DDL lub zaimportuj schemat w celu utworzenia żądanych obiektów i Przywróć poprzednią wartość parametru log_bin_trust_function_creators po utworzeniu.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>BŁĄD 1227 (42000) w wierszu 101: odmowa dostępu; potrzebujesz (co najmniej jednego z tych uprawnień) dla tej operacji. Operacja nie powiodła się z ExitCode 1

Powyższy błąd może wystąpić podczas importowania pliku zrzutu lub procedury tworzenia, która zawiera [definicje.](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) 

**Rozwiązanie**: Aby rozwiązać ten problem, administrator może udzielić uprawnień do tworzenia lub wykonywania procedur przez uruchomienie polecenia Grant, jak w następujących przykładach:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternatywnie można zastąpić definiujy nazwą użytkownika administracyjnego, który uruchamia proces importowania, jak pokazano poniżej.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>BŁĄD 1227 (42000) w wierszu 295: odmowa dostępu; potrzebujesz (co najmniej jednego z) uprawnień do wykonywania tej operacji lub SET_USER_ID

Powyższy błąd może wystąpić podczas wykonywania instrukcji CREATE VIEW with DEFINEs w ramach importowania pliku zrzutu lub uruchamiania skryptu. Azure Database for MySQL nie zezwala na żadne uprawnienia lub uprawnienia SET_USER_ID użytkownikom. 

**Rozwiązanie**: 
* Jeśli to możliwe, użyj użytkownika Zdefiniuj do wykonania operacji tworzenia. Prawdopodobnie istnieje wiele widoków z różnymi definicjami mającymi różne uprawnienia, co może być niemożliwe.  LUB
* Edytuj plik zrzutu lub Utwórz skrypt widoku i Usuń instrukcję DEFINE = z pliku zrzutu lub 
* Edytuj plik zrzutu lub Utwórz skrypt widoku i Zastąp wartości zdefiniowane przez użytkownika uprawnieniami administratora, które wykonują import lub wykonaj plik skryptu.

> [!Tip] 
> Użyj narzędzia SED lub Perl, aby zmodyfikować plik zrzutu lub skrypt SQL w celu zastąpienia instrukcji DEFINE =

## <a name="common-connection-errors-for-server-admin-login"></a>Typowe błędy połączeń dla logowania administratora serwera

Po utworzeniu serwera Azure Database for MySQL dane logowania administratora serwera są udostępniane przez użytkownika końcowego podczas tworzenia serwera. Identyfikator logowania administratora serwera umożliwia tworzenie nowych baz danych, dodawanie nowych użytkowników i przyznawanie uprawnień. Jeśli identyfikator logowania administratora serwera zostanie usunięty, jego uprawnienia zostaną odwołane lub jego hasło zostanie zmienione, podczas połączeń mogą pojawić się błędy połączeń w aplikacji. Poniżej wymieniono niektóre typowe błędy

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>BŁĄD 1045 (28000): odmowa dostępu dla użytkownika "Nazwa użytkownika" @ "adres IP" (przy użyciu hasła: tak)

Ten błąd występuje, jeśli:

* Nazwa użytkownika nie istnieje
* Nazwa użytkownika została usunięta
* jego hasło zostało zmienione lub zresetowane.

**Rozwiązanie**: 
* Sprawdź, czy "username" istnieje jako prawidłowy użytkownik na serwerze lub jest przypadkowo usunięty. Następujące zapytanie można wykonać, logując się do Azure Database for MySQL użytkownika:
  ```sql
  select user from mysql.user;
  ```
* Jeśli nie możesz zalogować się do programu MySQL, aby wykonać powyższe zapytanie, zalecamy [zresetowanie hasła administratora przy użyciu Azure Portal](howto-create-manage-server-portal.md). Opcja resetowania hasła w Azure Portal pomoże odtworzyć użytkownika, zresetować hasło i przywrócić uprawnienia administratora, co umożliwi zalogowanie się przy użyciu administratora serwera i wykonanie dalszych operacji.

## <a name="next-steps"></a>Następne kroki
Jeśli nie znajdziesz odpowiedzi, której szukasz, weź pod uwagę następujące kwestie:

- Opublikuj pytanie w witrynie [Microsoft Q&stronie pytania](/answers/topics/azure-database-mysql.html) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Wyślij wiadomość e-mail do Azure Database for MySQL zespołu [ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Ten adres e-mail nie jest aliasem pomocy technicznej.
- Skontaktuj się z pomocą techniczną platformy Azure, aby [uzyskać bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
