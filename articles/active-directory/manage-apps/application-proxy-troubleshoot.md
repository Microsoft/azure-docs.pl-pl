---
title: Rozwiązywanie problemów z serwerem proxy aplikacji | Microsoft Docs
description: Obejmuje rozwiązywanie problemów dotyczących błędów w usłudze Azure serwer proxy aplikacji usługi Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257967"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach

W przypadku rozwiązywania problemów z serwerem proxy aplikacji zalecamy rozpoczęcie od przejrzenia przepływu rozwiązywania problemów, [Debugowanie problemów dotyczących łącznika serwera proxy aplikacji](application-proxy-debug-connectors.md)w celu ustalenia, czy łączniki serwera proxy aplikacji są prawidłowo skonfigurowane. Jeśli nadal występują problemy z nawiązywaniem połączenia z aplikacją, postępuj zgodnie z przepływem rozwiązywania problemów, aby [debugować problemy aplikacji serwera proxy aplikacji](application-proxy-debug-apps.md).

Jeśli wystąpią błędy podczas uzyskiwania dostępu do opublikowanej aplikacji lub publikowania aplikacji, sprawdź poniższe opcje, aby sprawdzić, czy Microsoft Azure AD serwer proxy aplikacji działa prawidłowo:

* Otwórz konsolę usług systemu Windows. Sprawdź, czy usługa **łącznika serwera proxy aplikacji usługi Microsoft AAD** jest włączona i uruchomiona. Warto również zapoznać się ze stroną właściwości usługi serwera proxy aplikacji, jak pokazano na poniższej ilustracji:  
  ![Zrzut ekranu okno Właściwości łącznika serwera proxy aplikacji usługi Microsoft AAD](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otwórz Podgląd zdarzeń i Wyszukaj zdarzenia łącznika serwera proxy aplikacji w obszarze **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**  >  **admin**.
* W razie konieczności bardziej szczegółowe dzienniki są dostępne przez [włączenie dzienników sesji łącznika serwera proxy aplikacji](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Strona nie jest prawidłowo renderowana
Mogą występować problemy z renderowaniem aplikacji lub działać nieprawidłowo bez uzyskiwania określonych komunikatów o błędach. Taka sytuacja może wystąpić, jeśli opublikowano ścieżkę artykułu, ale aplikacja wymaga zawartości, która istnieje poza tą ścieżką.

Na przykład, Jeśli opublikujesz ścieżkę `https://yourapp/app` , ale aplikacja wywołuje obrazy w `https://yourapp/media` , nie będą renderowane. Upewnij się, że aplikacja została opublikowana przy użyciu ścieżki najwyższego poziomu, której potrzebujesz do uwzględnienia całej odpowiedniej zawartości. W tym przykładzie będzie to możliwe `http://yourapp/` .

## <a name="connector-errors"></a>Błędy łącznika

Jeśli rejestracja kończy się niepowodzeniem podczas instalacji kreatora łącznika, istnieją dwa sposoby wyświetlenia przyczyny niepowodzenia. W dzienniku zdarzeń w obszarze **aplikacje i usługi Logs\Microsoft\AadApplicationProxy\Connector\Admin** lub uruchom następujące polecenie programu Windows PowerShell:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Po znalezieniu błędu łącznika z dziennika zdarzeń Użyj tej tabeli typowych błędów, aby rozwiązać ten problem:

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Rejestracja łącznika nie powiodła się: Upewnij się, że włączono serwer proxy aplikacji w usłudze Azure portal zarządzania oraz że wprowadzono Active Directory nazwę użytkownika i hasło. Błąd: Wystąpił jeden lub więcej błędów. | Jeśli zamknięto okno rejestracji bez logowania do usługi Azure AD, ponownie uruchom Kreatora łącznika i zarejestruj łącznik. <br><br> Jeśli okno rejestracja zostanie otwarte, a następnie natychmiast zamknięte bez zezwolenia na zalogowanie, prawdopodobnie ten błąd wystąpi. Ten błąd występuje, gdy w systemie wystąpi błąd sieciowy. Upewnij się, że można nawiązać połączenie z przeglądarki z publiczną witryną sieci Web i że porty są otwarte zgodnie z [wymaganiami wstępnymi serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| W oknie rejestracji jest wyświetlany komunikat o błędzie "Wyczyść". Nie można przeprowadzić | Jeśli ten błąd jest wyświetlany, a następnie okno zostało zamknięte, wprowadzono nieprawidłową nazwę użytkownika lub hasło. Spróbuj ponownie. |
| Rejestracja łącznika nie powiodła się: Upewnij się, że włączono serwer proxy aplikacji w usłudze Azure portal zarządzania oraz że wprowadzono Active Directory nazwę użytkownika i hasło. Błąd: "AADSTS50059: nie znaleziono informacji identyfikacyjnych dzierżawy w żądaniu lub IMPLIKOWANYCH przez żadne podane poświadczenia i wyszukiwanie według nazwy głównej identyfikatora URI usługi nie powiodło się. | Podjęto próbę zalogowania się przy użyciu konta Microsoft, a nie domeny, która jest częścią identyfikatora organizacji katalogu, do którego próbujesz uzyskać dostęp. Upewnij się, że administrator jest częścią tej samej nazwy domeny co domena dzierżawy, na przykład jeśli domena usługi Azure AD to contoso.com, administrator powinien mieć wartość admin@contoso.com . |
| Nie można pobrać bieżących zasad wykonywania dla uruchomionych skryptów programu PowerShell. | Jeśli instalacja łącznika nie powiedzie się, upewnij się, że zasady wykonywania programu PowerShell nie są wyłączone. <br><br>1. Otwórz Edytor zasady grupy.<br>2. Przejdź do pozycji **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **PowerShell** i kliknij dwukrotnie pozycję **Włącz wykonywanie skryptów**.<br>3. zasady wykonywania można ustawić jako **Nieskonfigurowane** lub **włączone**. W przypadku wybrania opcji **włączone** upewnij się, że w obszarze Opcje zasady wykonywania są ustawione na **Zezwalaj na skrypty lokalne i zdalne podpisywane skrypty** lub **Zezwalaj na wszystkie skrypty**. |
| Pobranie konfiguracji przez łącznik nie powiodło się. | Certyfikat klienta łącznika, który jest używany na potrzeby uwierzytelniania, wygasł. Taka sytuacja może również wystąpić, jeśli masz łącznik zainstalowany za serwerem proxy. W takim przypadku łącznik nie może uzyskać dostępu do Internetu i nie będzie mógł udostępniać aplikacji użytkownikom zdalnym. Odnowić zaufanie ręcznie przy użyciu `Register-AppProxyConnector` polecenia cmdlet w programie Windows PowerShell. Jeśli łącznik znajduje się za serwerem proxy, konieczne jest przyznanie dostępu do Internetu kontom łącznika "usługi sieciowe" i "System lokalny". Można to osiągnąć przez przyznanie im dostępu do serwera proxy lub przez ustawienie ich w celu obejścia serwera proxy. |
| Rejestracja łącznika nie powiodła się: Upewnij się, że jesteś administratorem aplikacji Active Directory, aby zarejestrować łącznik. Błąd: "żądanie rejestracji zostało odrzucone." | Alias, za pomocą którego próbujesz się zalogować, nie jest administratorem w tej domenie. Łącznik jest zawsze instalowany dla katalogu, który jest właścicielem domeny użytkownika. Upewnij się, że konto administratora, za pomocą którego próbujesz się zalogować, ma co najmniej uprawnienia administratora aplikacji do dzierżawy usługi Azure AD. |
| Łącznik nie może nawiązać połączenia z usługą ze względu na problemy z siecią. Łącznik próbował uzyskać dostęp do następującego adresu URL. | Łącznik nie może nawiązać połączenia z usługą serwera proxy aplikacji w chmurze. Może się tak zdarzyć, jeśli masz regułę zapory blokującą połączenie. Upewnij się, że masz dozwolony dostęp do poprawnych portów i adresów URL wymienionych w [wymaganiach wstępnych serwera proxy aplikacji](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Błędy protokołu Kerberos

W tej tabeli opisano bardziej typowe błędy, które pochodzą z instalacji i konfiguracji protokołu Kerberos, a także przedstawiono sugestie dotyczące rozwiązania.

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Nie można pobrać bieżących zasad wykonywania dla uruchomionych skryptów programu PowerShell. | Jeśli instalacja łącznika nie powiedzie się, upewnij się, że zasady wykonywania programu PowerShell nie są wyłączone.<br><br>1. Otwórz Edytor zasady grupy.<br>2. Przejdź do pozycji **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **PowerShell** i kliknij dwukrotnie pozycję **Włącz wykonywanie skryptów**.<br>3. zasady wykonywania można ustawić jako **Nieskonfigurowane** lub **włączone**. W przypadku wybrania opcji **włączone** upewnij się, że w obszarze Opcje zasady wykonywania są ustawione na **Zezwalaj na skrypty lokalne i zdalne podpisywane skrypty** lub **Zezwalaj na wszystkie skrypty**. |
| 12008 — usługa Azure AD przekroczyła maksymalną liczbę dozwolonych prób uwierzytelnienia Kerberos do serwera wewnętrznej bazy danych. | Ten błąd może wskazywać na niepoprawną konfigurację między usługą Azure AD a serwerem aplikacji zaplecza lub problem w konfiguracji czasu i daty na obu komputerach. Serwer wewnętrznej bazy danych odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji zaplecza są prawidłowo skonfigurowane. Upewnij się, że konfiguracja czasu i daty w usłudze Azure AD i serwerze aplikacji zaplecza są zsynchronizowane. |
| 13016 — usługa Azure AD nie może pobrać biletu protokołu Kerberos w imieniu użytkownika, ponieważ nie ma nazwy UPN w tokenie brzegowym ani w pliku cookie dostępu. | Istnieje problem z konfiguracją usługi STS. Napraw Konfiguracja oświadczenia nazwy UPN w usłudze STS. |
| 13019 — usługa Azure AD nie może pobrać biletu protokołu Kerberos w imieniu użytkownika z powodu następującego ogólnego błędu interfejsu API. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem kontrolera domeny lub problem w konfiguracji czasu i daty na obu komputerach. Kontroler domeny odrzucił bilet Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji zaplecza są prawidłowo skonfigurowane, szczególnie w konfiguracji SPN. Upewnij się, że usługa Azure AD jest przyłączona do tej samej domeny, co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia relację zaufania z usługą Azure AD. Upewnij się, że konfiguracja czasu i daty w usłudze Azure AD i kontrolerze domeny są zsynchronizowane. |
| 13020 — usługa Azure AD nie może pobrać biletu protokołu Kerberos w imieniu użytkownika, ponieważ nie zdefiniowano nazwy SPN serwera wewnętrznej bazy danych. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem kontrolera domeny lub problem w konfiguracji czasu i daty na obu komputerach. Kontroler domeny odrzucił bilet Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji zaplecza są prawidłowo skonfigurowane, szczególnie w konfiguracji SPN. Upewnij się, że usługa Azure AD jest przyłączona do tej samej domeny, co kontroler domeny, aby upewnić się, że kontroler domeny ustanawia relację zaufania z usługą Azure AD. Upewnij się, że konfiguracja czasu i daty w usłudze Azure AD i kontrolerze domeny są zsynchronizowane. |
| 13022 — usługa Azure AD nie może uwierzytelnić użytkownika, ponieważ serwer zaplecza odpowiada na próby uwierzytelniania Kerberos z błędem HTTP 401. | To zdarzenie może wskazywać niepoprawną konfigurację między usługą Azure AD a serwerem aplikacji zaplecza lub problemem w konfiguracji czasu i daty na obu komputerach. Serwer wewnętrznej bazy danych odrzucił bilet protokołu Kerberos utworzony przez usługę Azure AD. Sprawdź, czy usługa Azure AD i serwer aplikacji zaplecza są prawidłowo skonfigurowane. Upewnij się, że konfiguracja czasu i daty w usłudze Azure AD i serwerze aplikacji zaplecza są zsynchronizowane. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z ograniczoną konfiguracją delegowania protokołu Kerberos dla serwera proxy aplikacji](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Błędy użytkownika końcowego

Ta lista zawiera błędy, które użytkownicy końcowi mogą napotkać podczas próby uzyskania dostępu do aplikacji i niepowodzenia. 

| Błąd | Zalecane czynności |
| ----- | ----------------- |
| Witryna sieci Web nie może wyświetlić strony. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, gdy aplikacja jest aplikacją IWA. Zdefiniowana nazwa SPN tej aplikacji może być niepoprawna. W przypadku aplikacji IWA upewnij się, że nazwa SPN skonfigurowana dla tej aplikacji jest prawidłowa. |
| Witryna sieci Web nie może wyświetlić strony. | Użytkownik może uzyskać ten błąd podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli aplikacja jest aplikacją OWA. Może to być spowodowane jedną z następujących czynności:<br><li>Zdefiniowana nazwa SPN dla tej aplikacji jest niepoprawna. Upewnij się, że nazwa SPN skonfigurowana dla tej aplikacji jest poprawna.</li><li>Użytkownik próbujący uzyskać dostęp do aplikacji używa konto Microsoft, a nie odpowiedniego konta firmowego do zalogowania się lub użytkownik jest użytkownikiem-gościem. Upewnij się, że użytkownik loguje się przy użyciu konta firmowego zgodnego z domeną opublikowanej aplikacji. Użytkownicy i Goście konta Microsoft nie mogą uzyskać dostępu do aplikacji IWA.</li><li>Użytkownik próbujący uzyskać dostęp do aplikacji nie został poprawnie zdefiniowany dla tej aplikacji na stronie lokalnej. Upewnij się, że ten użytkownik ma odpowiednie uprawnienia zdefiniowane dla tej aplikacji zaplecza na maszynie lokalnej. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, że użytkownik ma przypisany dostęp do tej aplikacji. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji w przypadku korzystania z kont Microsoft zamiast konta firmowego w celu zalogowania się. Ten błąd może również zostać wyświetlony przez użytkowników-Gości. Użytkownicy i Goście konta Microsoft nie mogą uzyskać dostępu do aplikacji IWA. Upewnij się, że użytkownik loguje się przy użyciu konta firmowego zgodnego z domeną opublikowanej aplikacji.<br><br>Użytkownik nie ma przypisanego użytkownika do tej aplikacji. Przejdź do karty **aplikacja** i w obszarze **Użytkownicy i grupy** Przypisz tego użytkownika lub grupę użytkowników do tej aplikacji. |
| Nie można teraz uzyskać dostępu do tej aplikacji firmowej. Spróbuj ponownie później... Przekroczono limit czasu łącznika. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie są one prawidłowo zdefiniowane dla tej aplikacji na stronie lokalnej. Upewnij się, że użytkownicy mają odpowiednie uprawnienia zdefiniowane dla tej aplikacji zaplecza na maszynie lokalnej. |
| Nie można uzyskać dostępu do tej aplikacji firmowej. Nie masz uprawnień dostępu do tej aplikacji. Autoryzacja nie powiodła się. Upewnij się, że użytkownik ma licencję na Azure Active Directory — wersja Premium. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli nie zostały one jawnie przypisane do licencji Premium przez administratora subskrybenta. Przejdź do karty Active Directory **licencji** na subskrybencie i upewnij się, że ten użytkownik lub ta grupa użytkowników ma przypisaną licencję Premium. |
| Nie można znaleźć serwera o określonej nazwie hosta. | Ten błąd może wystąpić podczas próby uzyskania dostępu do opublikowanej aplikacji, jeśli domena niestandardowa aplikacji nie jest prawidłowo skonfigurowana. Upewnij się, że został przekazany certyfikat dla domeny i poprawnie skonfigurowano rekord DNS, wykonując kroki opisane w temacie [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) |
|Zabronione: nie można uzyskać dostępu do tej aplikacji firmowej lub nie można autoryzować użytkownika. Upewnij się, że użytkownik jest zdefiniowany w lokalnej usłudze AD i że użytkownik ma dostęp do aplikacji w lokalnej usłudze AD. | Może to być problem z dostępem do informacji o autoryzacji, zobacz [niektóre aplikacje i interfejsy API wymagają dostępu do informacji o autoryzacji obiektów konta]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). W Nutshell należy dodać konto komputera łącznika serwera proxy aplikacji do grupy domeny wbudowanej grupy dostępu autoryzacji systemu Windows, aby rozwiązać ten problem. |

## <a name="my-error-wasnt-listed-here"></a>Mój błąd nie został tutaj wymieniony

Jeśli wystąpi błąd lub problem z usługą Azure serwer proxy aplikacji usługi Azure AD, która nie jest wymieniona w tym przewodniku rozwiązywania problemów, chcemy wiedzieć o tym. Wyślij wiadomość e-mail do [zespołu ds. opinii](mailto:aadapfeedback@microsoft.com) z informacjami o napotkanym błędzie.

## <a name="see-also"></a>Zobacz też
* [Włącz serwer proxy aplikacji dla Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikowanie aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md)
* [Włącz logowanie jednokrotne](application-proxy-configure-single-sign-on-with-kcd.md)
* [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
