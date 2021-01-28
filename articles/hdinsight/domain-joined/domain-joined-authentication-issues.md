---
title: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
description: Problemy z uwierzytelnianiem w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: a0ca7cb8797b90d8cf933733c48be299e79be8aa
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933620"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemy z uwierzytelnianiem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

W przypadku bezpiecznych klastrów objętych usługą Azure Data Lake (Gen1 lub Gen2), gdy użytkownicy domeny logują się do usług klastra za pośrednictwem bramy HDI (na przykład zalogowania się do portalu Apache Ambari), program HDI Gateway spróbuje najpierw uzyskać token OAuth od Azure Active Directory (Azure AD), a następnie uzyskać bilet protokołu Kerberos z usługi Azure AD DS. Uwierzytelnianie może zakończyć się niepowodzeniem w żadnym z tych etapów. Ten artykuł ma na celu debugowanie niektórych z tych problemów.

Gdy uwierzytelnianie nie powiedzie się, zostanie wyświetlony monit o podanie poświadczeń. Jeśli anulujesz to okno dialogowe, zostanie wydrukowany komunikat o błędzie. Poniżej przedstawiono niektóre typowe komunikaty o błędach:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant lub unauthorized_client, 50126

### <a name="issue"></a>Problem

Logowanie użytkowników federacyjnych kończy się niepowodzeniem z kodem błędu 50126 (logowanie dla użytkowników w chmurze kończy się niepowodzeniem). Komunikat o błędzie jest podobny do:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Przyczyna

Kod błędu usługi Azure AD 50126 oznacza, że `AllowCloudPasswordValidation` zasady nie zostały ustawione przez dzierżawcę.

### <a name="resolution"></a>Rozwiązanie

Administrator firmy w dzierżawie usługi Azure AD powinien zezwolić usłudze Azure AD na używanie skrótów haseł dla użytkowników z kopiami zapasowymi usług ADFS.  Zastosuj, `AllowCloudPasswordValidationPolicy` jak pokazano w artykule, [Użyj pakiet Enterprise Security w usłudze HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant lub unauthorized_client, 50034

### <a name="issue"></a>Problem

Logowanie kończy się niepowodzeniem z kodem błędu 50034. Komunikat o błędzie jest podobny do:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Przyczyna

Nazwa użytkownika jest niepoprawna (nie istnieje). Użytkownik nie korzysta z tej samej nazwy użytkownika, która jest używana w Azure Portal.

### <a name="resolution"></a>Rozwiązanie

Użyj tej samej nazwy użytkownika, która działa w tym portalu.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant lub unauthorized_client, 50053

### <a name="issue"></a>Problem

Konto użytkownika jest zablokowane, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Przyczyna

Zbyt wiele prób logowania przy użyciu niepoprawnego hasła.

### <a name="resolution"></a>Rozwiązanie

Zaczekaj 30 minut lub tak, Zatrzymaj wszystkie aplikacje, które mogą próbować uwierzytelniać.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant lub unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problem

Hasło wygasło, kod błędu 50053. Komunikat o błędzie jest podobny do:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Przyczyna

Hasło wygasło.

### <a name="resolution"></a>Rozwiązanie

Zmień hasło w Azure Portal (w systemie lokalnym), a następnie poczekaj 30 minut na przechwycenie synchronizacji.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problem

Odbieranie komunikatu o błędzie `interaction_required` .

### <a name="cause"></a>Przyczyna

Zasady dostępu warunkowego lub uwierzytelniania wieloskładnikowego są stosowane do użytkownika. Ponieważ uwierzytelnianie interakcyjne nie jest jeszcze obsługiwane, użytkownika lub klaster należy wykluczyć z uwierzytelniania wieloskładnikowego lub dostępu warunkowego. Jeśli zdecydujesz się na wykluczenie klastra (Zasady wykluczania oparte na adresach IP), upewnij się, że Usługa AD `ServiceEndpoints` jest włączona dla tej sieci wirtualnej.

### <a name="resolution"></a>Rozwiązanie

Użyj zasad dostępu warunkowego i Wyklucz klastry HDInisght z usługi MFA, jak pokazano w temacie [Konfigurowanie klastra usługi HDInsight z pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Odmowa logowania

### <a name="issue"></a>Problem

Odmowa logowania.

### <a name="cause"></a>Przyczyna

Aby przejść do tego etapu, uwierzytelnianie OAuth nie jest problemem, ale uwierzytelnianie Kerberos to. Jeśli ten klaster jest objęty ADLS, logowanie OAuth zakończyło się pomyślnie przed podjęciem próby uwierzytelnienia Kerberos. W klastrach WASB nie podjęto próby logowania przy użyciu protokołu OAuth. Może istnieć wiele powodów użycia protokołu Kerberos skróty haseł, które nie są zsynchronizowane, konto użytkownika zostało zablokowane na platformie Azure AD DS i tak dalej. Skróty haseł synchronizują się tylko wtedy, gdy użytkownik zmienia hasło. Podczas tworzenia wystąpienia usługi Azure AD DS rozpocznie się Synchronizowanie haseł, które zostały zmienione po utworzeniu. Nie synchronizuje wstecz hasła, które zostały ustawione przed jego rozpoczęciem.

### <a name="resolution"></a>Rozwiązanie

Jeśli uważasz, że hasła mogą nie być zsynchronizowane, spróbuj zmienić hasło i poczekaj kilka minut na synchronizację.

Spróbuj użyć protokołu SSH w celu uwierzytelnienia (Narzędzie kinit) przy użyciu tych samych poświadczeń użytkownika, z komputera, który jest przyłączony do domeny. Użyj protokołu SSH do węzła głowy/krawędzi z użytkownikiem lokalnym, a następnie uruchom narzędzie kinit.

---

## <a name="kinit-fails"></a>Narzędzie kinit kończy się niepowodzeniem

### <a name="issue"></a>Problem

Narzędzie kinit kończy się niepowodzeniem.

### <a name="cause"></a>Przyczyna

Różni się.

### <a name="resolution"></a>Rozwiązanie

Aby narzędzie kinit się powieść, musisz znać `sAMAccountName` (to jest krótka nazwa konta bez obszaru). `sAMAccountName` jest zazwyczaj prefiksem konta (na przykład Roberta in `bob@contoso.com` ). W przypadku niektórych użytkowników może się to różnić. Będziesz potrzebować możliwości przeglądania i przeszukiwania katalogu w celu uzyskania informacji `sAMAccountName` .

Sposoby znajdowania `sAMAccountName` :

* Jeśli możesz zalogować się do usługi Ambari przy użyciu lokalnego administratora usługi Ambari, zapoznaj się z listą użytkowników.

* Jeśli masz [przyłączoną do domeny maszynę z systemem Windows](../../active-directory-domain-services/tutorial-create-management-vm.md), możesz użyć standardowych narzędzi Windows AD do przeglądania. Wymaga to konta działającego w domenie.

* W węźle głównym można wyszukiwać za pomocą poleceń SAMBA. Wymaga to prawidłowej sesji protokołu Kerberos (pomyślne narzędzie kinit). Wyszukiwanie w usłudze net AD "(userPrincipalName = Robert *)"

    W wynikach wyszukiwania/przeglądania powinna być wyświetlana wartość `sAMAccountName` atrybutu. Ponadto można przeglądać inne atrybuty, takie jak `pwdLastSet` , `badPasswordTime` `userPrincipalName` itp., aby sprawdzić, czy te właściwości są zgodne z oczekiwaniami.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>Narzędzie kinit kończy się niepowodzeniem z powodu niepowodzenia uwierzytelniania wstępnego

### <a name="issue"></a>Problem

Narzędzie kinit kończy się niepowodzeniem z `Preauthentication` powodu błędu.

### <a name="cause"></a>Przyczyna

Nieprawidłowa nazwa użytkownika lub hasło.

### <a name="resolution"></a>Rozwiązanie

Sprawdź nazwę użytkownika i hasło. Sprawdź również inne opisane powyżej właściwości. Aby włączyć pełne debugowanie, uruchom polecenie `export KRB5_TRACE=/tmp/krb.log` z sesji przed podjęciem próby narzędzie kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Polecenie Job/HDFS nie powiodło się z powodu TokenNotFoundException

### <a name="issue"></a>Problem

Polecenie Job/HDFS nie powiodło się z powodu błędu `TokenNotFoundException` .

### <a name="cause"></a>Przyczyna

Nie znaleziono wymaganego tokenu dostępu OAuth, aby zadanie/polecenie zakończyło się pomyślnie. Sterownik ADLS/ABFS podejmie próbę pobrania tokenu dostępu OAuth z usługi Credential przed przekazaniem żądań magazynu. Ten token jest rejestrowany po zalogowaniu się do portalu Ambari przy użyciu tego samego użytkownika.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że po pomyślnym zalogowaniu się do portalu Ambari za pomocą nazwy użytkownika, której tożsamość jest używana do uruchomienia zadania.

---

## <a name="error-fetching-access-token"></a>Błąd podczas pobierania tokenu dostępu

### <a name="issue"></a>Problem

Użytkownik otrzymuje komunikat o błędzie `Error fetching access token` .

### <a name="cause"></a>Przyczyna

Ten błąd występuje sporadycznie, gdy użytkownicy próbują uzyskać dostęp do ADLS Gen2 przy użyciu list kontroli dostępu, a token Kerberos wygasł.

### <a name="resolution"></a>Rozwiązanie

* W przypadku Azure Data Lake Storage Gen1, wyczyść pamięć podręczną przeglądarki i ponownie zaloguj się do Ambari.

* W przypadku Azure Data Lake Storage Gen2 Uruchom `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` dla użytkownika, w którym użytkownik próbuje się zalogować

---

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]