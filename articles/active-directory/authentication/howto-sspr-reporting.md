---
title: Raporty funkcji samoobsługowego resetowania haseł — Azure Active Directory
description: Raportowanie zdarzeń samoobsługowego resetowania hasła w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6aa31d0b8c68f38d2b4fc41a6bbaaeab8a3ffac
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363610"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Opcje raportowania dotyczące zarządzania hasłami w usłudze Azure AD)

Po wdrożeniu wiele organizacji chce wiedzieć, jak i w przypadku użycia funkcji samoobsługowego resetowania hasła (SSPR). Funkcja raportowania, która Azure Active Directory (Azure AD) zapewnia odpowiedzi na pytania przy użyciu prekompilowanych raportów. Jeśli masz odpowiednie licencje, możesz również utworzyć niestandardowe zapytania.

![Raportowanie w usłudze SSPR przy użyciu dzienników inspekcji w usłudze Azure AD][Reporting]

Raporty, które istnieją w [Azure Portal](https://portal.azure.com/)mogą uzyskać odpowiedzi na następujące pytania:

> [!NOTE]
> Musisz być [administratorem globalnym](../roles/permissions-reference.md), aby te dane były zbierane w imieniu organizacji. Aby zrezygnować z programu, należy odwiedzić kartę **raportowanie** lub dzienniki inspekcji co najmniej raz. Do tego momentu dane nie będą zbierane dla Twojej organizacji.
>

* Ile osób zostało zarejestrowanych do resetowania haseł?
* Kto został zarejestrowany do resetowania hasła?
* Jakie dane są rejestrowanie osób?
* Ile osób resetuje swoje hasła w ciągu ostatnich siedmiu dni?
* Jakie są najczęstsze metody resetowania haseł przez użytkowników lub administratorów?
* Jakie są typowe problemy związane z użytkownikami lub administratorami podczas próby użycia resetowania hasła?
* Jakie Administratorzy często resetuje własne hasła?
* Czy istnieją podejrzane działania z resetowaniem hasła?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jak wyświetlać raporty dotyczące zarządzania hasłami w witrynie Azure Portal

W Azure Portal środowisku ulepszono sposób wyświetlania operacji resetowania haseł i rejestrowania resetowania haseł. Wykonaj poniższe czynności, aby znaleźć zdarzenia resetowania hasła i rejestrowania resetowania hasła:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**.
3. Wyszukaj **Azure Active Directory** na liście usług i wybierz ją.
4. Wybierz pozycję **Użytkownicy** z sekcji Zarządzanie.
5. Wybierz pozycję **dzienniki inspekcji** w bloku **Użytkownicy** . Przedstawiono w nim wszystkie zdarzenia inspekcji, które wystąpiły dla wszystkich użytkowników w katalogu. Możesz filtrować ten widok, aby wyświetlić wszystkie zdarzenia związane z hasłem.
6. Z menu **Filtr** w górnej części okienka wybierz listę rozwijaną **Usługa** i zmień ją na typ usługi **zarządzania hasłami samoobsługowych** .
7. Opcjonalnie możesz dodatkowo odfiltrować listę, wybierając odpowiednie **działanie** .

### <a name="combined-registration"></a>Rejestracja łączona

Jeśli włączono [rejestrację łączną](./concept-registration-mfa-sspr-combined.md), informacje dotyczące aktywności użytkownika w dziennikach inspekcji będą znajdować się **Security**w obszarze  >  **metody uwierzytelniania**zabezpieczeń.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Opis kolumn raportu w Azure Portal

Na poniższej liście opisano szczegółowo poszczególne kolumny raportu w Azure Portal:

* **Użytkownik**: użytkownik, który próbował wykonać operację rejestracji resetowania hasła.
* **Rola**: rola użytkownika w katalogu.
* **Data i godzina**: Data i godzina próby.
* **Zarejestrowane dane**: dane uwierzytelniania podane przez użytkownika podczas rejestracji resetowania hasła.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Opis wartości raportu w Azure Portal

W poniższej tabeli opisano różne wartości, które można ustawić dla każdej kolumny w Azure Portal:

| Kolumna | Dozwolone wartości i ich znaczenie |
| --- | --- |
| Zarejestrowane dane |**Alternatywny adres e-mail**: użytkownik użył alternatywnego adresu e-mail lub wiadomości e-mail do uwierzytelnienia.<p><p>**Telefon biurowy**: użytkownik użył telefonu biurowego do uwierzytelnienia.<p>**Telefon komórkowy**: użytkownik użył telefonu komórkowego lub telefonu uwierzytelniania do uwierzytelnienia.<p>**Pytania zabezpieczające**: użytkownik użył pytań zabezpieczających do uwierzytelnienia.<p>**Dowolna kombinacja poprzednich metod, na przykład alternatywnej poczty e-mail i telefonu komórkowego**: występuje, gdy określono zasady dwubramowe i pokazano dwie metody używane do uwierzytelniania żądania resetowania hasła. |

## <a name="self-service-password-management-activity-types"></a>Self-Service typów działań związanych z zarządzaniem hasłami

Następujące typy działań są wyświetlane w kategorii zdarzenia inspekcji **samoobsługowego zarządzania hasłami** :

* Zablokowany do samoobsługowego [resetowania hasła](#activity-type-blocked-from-self-service-password-reset): wskazuje, że użytkownik próbował zresetować hasło, użyć określonej bramy lub zweryfikować numer telefonu więcej niż pięć razy w ciągu 24 godzin.
* [Zmień hasło (samoobsługa)](#activity-type-change-password-self-service): wskazuje, że użytkownik wykonał niezależną lub wymuszoną (z powodu wygaśnięcia) zmianę hasła.
* [Resetowanie hasła (przez administratora)](#activity-type-reset-password-by-admin): wskazuje, że administrator przeprowadził Resetowanie hasła w imieniu użytkownika z Azure Portal.
* [Resetowanie hasła (samoobsługa)](#activity-type-reset-password-self-service): wskazuje, że użytkownik pomyślnie zresetował swoje hasło z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* [Postęp działania przepływu samoobsługowego resetowania hasła](#activity-type-self-serve-password-reset-flow-activity-progress): wskazuje każdy określony krok, przez który użytkownik przechodzi przez użytkownika, na przykład przekazanie konkretnej bramy uwierzytelniania resetowania hasła w ramach procesu resetowania hasła.
* [Odblokuj konto użytkownika (samoobsługa)](#activity-type-unlock-a-user-account-self-service)): wskazuje, że użytkownik pomyślnie odblokował konto Active Directory bez resetowania hasła w [portalu resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu funkcji Active Directory odblokowywania konta bez zresetowania.
* [Użytkownik zarejestrowany do samoobsługowego resetowania hasła](#activity-type-user-registered-for-self-service-password-reset): wskazuje, że użytkownik zarejestrował wszystkie wymagane informacje, aby móc zresetować swoje hasło zgodnie z aktualnie określonymi zasadami resetowania hasła dzierżawy.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ działania: zablokowany do samoobsługowego resetowania hasła

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że użytkownik próbował zresetować hasło, użyć określonej bramy lub zweryfikować numer telefonu więcej niż pięć razy w ciągu 24 godzin.
* **Aktor aktywności**: użytkownik, który został ograniczony do wykonywania dodatkowych operacji resetowania. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który został ograniczony do wykonywania dodatkowych operacji resetowania. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stan działania**:
  * _Sukces_: wskazuje, że użytkownik został ograniczony do wykonywania dodatkowych operacji resetowania, podjęto próbę wykonania dodatkowych metod uwierzytelniania lub weryfikacji wszelkich dodatkowych numerów telefonów w ciągu następnych 24 godzin.
* **Przyczyna niepowodzenia stanu działania**: nie dotyczy.

### <a name="activity-type-change-password-self-service"></a>Typ działania: zmiana hasła (samoobsługa)

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że użytkownik wykonał nieprawidłową lub wymuszone (z powodu wygaśnięcia) hasło.
* **Aktor aktywności**: użytkownik, który zmienił swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który zmienił swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany działań**:
  * _Sukces_: wskazuje, że użytkownik pomyślnie zmienił swoje hasło.
  * _Niepowodzenie_: wskazuje, że użytkownik nie mógł zmienić hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.
* **Przyczyna niepowodzenia stanu działania**:
  * _FuzzyPolicyViolationInvalidPassword_: użytkownik wybrał hasło, które zostało automatycznie zablokowane, ponieważ możliwości wykrywania wykluczonych haseł przez firmę Microsoft okazały się zbyt popularne lub szczególnie słabe.

### <a name="activity-type-reset-password-by-admin"></a>Typ działania: Resetowanie hasła (przez administratora)

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że administrator wykonał Resetowanie hasła w imieniu użytkownika z Azure Portal.
* **Aktor aktywności**: administrator, który wykonał Resetowanie hasła w imieniu innego użytkownika lub administratora końcowego. Musi być administratorem hasła, administratorem użytkownika lub administratorem pomocy technicznej.
* **Cel działania**: użytkownik, którego hasło zostało zresetowane. Użytkownik może być użytkownikiem końcowym lub innym administratorem.
* **Stany działań**:
  * _Sukces_: wskazuje, że administrator pomyślnie zresetował hasło użytkownika.
  * _Niepowodzenie_: wskazuje, że administrator nie może zmienić hasła użytkownika. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.

### <a name="activity-type-reset-password-self-service"></a>Typ działania: Resetowanie hasła (samoobsługa)

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że użytkownik pomyślnie zresetował swoje hasło z [portalu resetowania hasła usługi Azure AD](https://passwordreset.microsoftonline.com).
* **Aktor aktywności**: użytkownik, który zresetuje swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który zresetuje swoje hasło. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany działań**:
  * _Sukces_: wskazuje, że użytkownik pomyślnie zresetował własne hasło.
  * _Niepowodzenie_: wskazuje, że użytkownik nie mógł zresetować własnego hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.
* **Przyczyna niepowodzenia stanu działania**:
  * _FuzzyPolicyViolationInvalidPassword_: Administrator zaznaczył hasło, które zostało automatycznie zablokowane, ponieważ możliwości wykrywania wykluczonych haseł przez firmę Microsoft okazały się zbyt popularne lub szczególnie słabe.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ działania: zapełnienie postępu działania przepływu resetowania hasła

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje każdy określony krok, przez który użytkownik przechodzi przez użytkownika (na przykład przekazanie bramy uwierzytelniania określonego hasła) w ramach procesu resetowania hasła.
* **Aktor aktywności**: użytkownik, który wykonał część przepływu resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który wykonał część przepływu resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Stany działań**:
  * _Sukces_: wskazuje, że użytkownik pomyślnie ukończył określony krok przepływu resetowania hasła.
  * _Niepowodzenie_: wskazuje, że określony krok przepływu resetowania hasła nie powiódł się. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.
* **Przyczyny stanu działania**: Zapoznaj się z poniższą tabelą dla [wszystkich przyczyn stanu działania resetowania](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ działania: odblokowanie konta użytkownika (samoobsługa)

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że użytkownik pomyślnie odblokował konto Active Directory bez resetowania hasła w [portalu resetowania haseł usługi Azure AD](https://passwordreset.microsoftonline.com) przy użyciu funkcji Active Directory odblokowywania konta bez zresetowania.
* **Aktor aktywności**: użytkownik, który odblokował swoje konto bez resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który odblokował swoje konto bez resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Dozwolone Stany działań**:
  * _Sukces_: wskazuje, że użytkownik pomyślnie odblokował własne konto.
  * _Niepowodzenie_: wskazuje, że użytkownik nie mógł odblokować konta. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ działania: Użytkownik zarejestrowany do samoobsługowego resetowania hasła

Poniższa lista zawiera szczegółowy opis tego działania:

* **Opis działania**: wskazuje, że użytkownik zarejestrował wszystkie wymagane informacje, aby móc zresetować swoje hasło zgodnie z aktualnie określonymi zasadami resetowania hasła dzierżawy. 
* **Aktor aktywności**: użytkownik, który zarejestrował się do resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Cel działania**: użytkownik, który zarejestrował się do resetowania hasła. Użytkownik może być użytkownikiem końcowym lub administratorem.
* **Dozwolone Stany działań**:
  * _Sukces_: wskazuje, że użytkownik został pomyślnie zarejestrowany do resetowania hasła zgodnie z bieżącymi zasadami. 
  * _Niepowodzenie_: wskazuje, że użytkownik nie mógł zarejestrować się w celu zresetowania hasła. Możesz wybrać wiersz, aby wyświetlić kategorię **Przyczyna stanu działania** , aby dowiedzieć się więcej o tym, dlaczego wystąpił błąd.

     >[!NOTE]
     >Niepowodzenie nie oznacza, że użytkownik nie może zresetować własnego hasła. Oznacza to, że nie zakończył procesu rejestracji. Jeśli na koncie są niezweryfikowane dane, które są poprawne, takie jak numer telefonu, który nie został zweryfikowany, mimo że nie zweryfikowano tego numeru telefonu, nadal może on używać go do resetowania hasła.

## <a name="next-steps"></a>Następne kroki

* [SSPR i raportowanie informacji dotyczących użycia i usługi MFA](howto-authentication-methods-usage-insights.md)
* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Zarejestruj się w celu samoobsługowego resetowania hasła](../user-help/active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](./tutorial-enable-sspr-writeback.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Przykład dzienników inspekcji działań SSPR w usłudze Azure AD"