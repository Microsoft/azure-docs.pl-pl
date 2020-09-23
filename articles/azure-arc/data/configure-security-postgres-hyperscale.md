---
title: Skonfiguruj zabezpieczenia dla grupy serwerów PostgreSQL na potrzeby usługi Azure Arc
description: Skonfiguruj zabezpieczenia dla grupy serwerów PostgreSQL na potrzeby usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b166348031e9f72e8005e866a198855db9c01a9c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939211"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Skonfiguruj zabezpieczenia dla grupy serwerów PostgreSQL na potrzeby usługi Azure Arc

W tym dokumencie opisano różne aspekty związane z bezpieczeństwem grupy serwerów:
- Szyfrowanie w spoczynku
- Zarządzanie użytkownikami
   - Perspektywy ogólne
   - Zmień hasło użytkownika administracyjnego _Postgres_

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
Szyfrowanie można zaimplementować w spoczynku, szyfrując dyski, na których przechowywane są bazy danych, i/lub używając funkcji bazy danych, aby zaszyfrować wstawiane lub aktualizowane dane.

### <a name="hardware-linux-host-volume-encryption"></a>Sprzęt: szyfrowanie woluminu hosta systemu Linux
Zaimplementuj szyfrowanie danych systemowych, aby zabezpieczyć wszystkie dane, które znajdują się na dyskach używanych przez usługę Azure ARC z włączonym Data Services instalacji. Więcej informacji na ten temat można znaleźć w temacie:
- Ogólnie [szyfrowanie danych](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) w systemie Linux 
- Szyfrowanie dysków za pomocą `cryptsetup` polecenia szyfrowania luks (Linux) (w https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) zależności od tego, czy usługa Azure ARC z włączonym Data Services jest uruchamiana w udostępnionej infrastrukturze fizycznej, użytkownik jest odpowiedzialny za Zabezpieczanie infrastruktury.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Oprogramowanie: Użyj rozszerzenia PostgreSQL `pgcrypto` w grupie serwerów
Oprócz szyfrowania dysków używanych do hostowania Twojego ustawienia usługi Azure Arc można skonfigurować grupę serwerów PostgreSQL z funkcją Azure Arc w celu udostępnienia mechanizmów, które mogą być używane przez aplikacje do szyfrowania danych w bazie danych. `pgcrypto`Rozszerzenie jest częścią `contrib` rozszerzeń Postgres i jest dostępne w grupie serwerów PostgreSQL w usłudze Azure Arc. Szczegółowe informacje o rozszerzeniu znajdują się `pgcrypto` [tutaj](https://www.postgresql.org/docs/current/pgcrypto.html).
Podsumowując, przy użyciu następujących poleceń, można je utworzyć i użyć:


#### <a name="create-the-pgcrypto-extension"></a>Utwórz `pgcrypto` rozszerzenie
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
CREATE EXTENSION pgcrypto;
```

> [Szczegółowe informacje](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) na temat sposobu nawiązywania połączenia.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Sprawdź listę rozszerzeń, które są gotowe do użycia w grupie serwerów
Możesz sprawdzić, czy `pgcrypto` rozszerzenie jest gotowe do użycia, wyświetlając listę rozszerzeń dostępnych w grupie serwerów.
Połącz się z grupą serwerów za pomocą wybranego narzędzia klienckiego i uruchom standardowe zapytanie PostgreSQL:
```console
select * from pg_extension;
```
Należy sprawdzić, `pgcrypto` czy został on włączony i utworzony za pomocą poleceń wskazanych powyżej.

#### <a name="use-the-pgcrypto-extension"></a>Użyj `pgcrypto` rozszerzenia
Teraz możesz dostosować kod aplikacji tak, aby korzystały z dowolnej z funkcji oferowanych przez `pgcrypto` :
- Ogólne funkcje wyznaczania wartości skrótu
- Funkcje tworzenia skrótów haseł
- Funkcje szyfrowania PGP
- Nieprzetworzone funkcje szyfrowania
- Funkcje danych losowych

Na przykład, aby wygenerować wartości skrótu. Uruchom polecenie:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Zwraca następujący skrót:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Lub na przykład:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

zwraca następujący skrót:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Lub, na przykład, do przechowywania zaszyfrowanych danych, takich jak hasło:

Załóżmy, że moja aplikacja przechowuje wpisy tajne w poniższej tabeli:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

I szyfruję swoje hasło podczas tworzenia użytkownika:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Teraz widać, że moje hasło jest zaszyfrowane:

```console
select * from mysecrets;
```

Dane wyjściowe:

- Identyfikator użytkownika: 1
- Nazwa użytkownika: Me
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Po nawiązaniu połączenia z moją aplikacją i przekazaniem hasła będzie ono znajdować się w `mysecrets` tabeli i zwróci nazwę użytkownika w przypadku dopasowania hasła dostarczonego do aplikacji i haseł przechowywanych w tabeli. Przykład:

- Podano nieprawidłowe hasło:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Dane wyjściowe 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Przeszedł poprawne hasło:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Dane wyjściowe:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Ten mały przykład pokazuje, że można zaszyfrować dane w spoczynku (Przechowuj dane zaszyfrowane) w usłudze Azure ARC z włączonym skalowaniem PostgreSQL przy użyciu `pgcrypto` rozszerzenia Postgres, a aplikacje mogą używać funkcji oferowanych przez program `pgcrypto` do manipulowania zaszyfrowanymi danymi.

## <a name="user-management"></a>Zarządzanie użytkownikami
### <a name="general-perspectives"></a>Perspektywy ogólne
Możesz użyć standardowego Postgres, aby utworzyć użytkowników lub role. Jeśli jednak to zrobisz, te artefakty będą dostępne tylko w roli koordynatora. W trakcie okresu zapoznawczego użytkownicy/role nie będą mogli uzyskać dostępu do danych dystrybuowanych poza węzłem koordynatora i węzłami procesu roboczego grupy serwerów. Przyczyną jest to, że w wersji zapoznawczej definicja użytkownika nie jest replikowana do węzłów procesu roboczego.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Zmień hasło użytkownika administracyjnego _Postgres_
Usługa Azure ARC z możliwością skalowania w PostgreSQL jest dostępna ze standardowym użytkownikiem administracyjnym Postgres _Postgres_ , dla którego można ustawić hasło podczas tworzenia grupy serwerów.
Ogólny format polecenia zmiany hasła:
```console
azdata arc postgres server edit --name <server group name> --admin-password <new password>
```
Jeśli istnieje, hasło zostanie ustawione na wartość zmiennej środowiskowej **sesji**AZDATA_PASSWORD. Jeśli nie, użytkownik zostanie poproszony o podanie wartości.
Aby sprawdzić, czy zmienna środowiskowa sesji AZDATA_PASSWORD istnieje i/lub do jakiej wartości jest ustawiona, uruchom polecenie:
```console
printenv AZDATA_PASSWORD
```
Możesz chcieć usunąć jego wartość, jeśli wolisz monitować o wprowadzenie nowego hasła.


## <a name="next-steps"></a>Następne kroki
- Przeczytaj szczegółowe informacje o `pgcrypto` rozszerzeniu. [here](https://www.postgresql.org/docs/current/pgcrypto.html)
- Przeczytaj szczegółowe informacje o [tym](using-extensions-in-postgresql-hyperscale-server-group.md), jak używać rozszerzeń Postgres.

