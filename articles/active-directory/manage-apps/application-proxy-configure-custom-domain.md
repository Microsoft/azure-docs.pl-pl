---
title: Domeny niestandardowe na platformie Azure serwer proxy aplikacji usługi Azure AD
description: Konfigurowanie domen niestandardowych i zarządzanie nimi w usłudze Azure serwer proxy aplikacji usługi Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 49edab5ed20749cb705d160e5cdc46a16bdee951
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258035"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurowanie domen niestandardowych za pomocą serwera proxy aplikacji w usłudze Azure AD

Po opublikowaniu aplikacji za poorednictwem serwer proxy aplikacji usługi Azure Active Directory należy utworzyć zewnętrzny adres URL dla użytkowników. Ten adres URL pobiera domyślną domenę *yourtenant.msappproxy.NET*. Na przykład jeśli opublikujesz aplikację o nazwie *wydatki* w dzierżawie o nazwie *contoso*, zewnętrzny adres URL to *https: \/ /Expenses-contoso.msappproxy.NET*. Jeśli chcesz użyć własnej nazwy domeny zamiast *msappproxy.NET*, możesz skonfigurować domenę niestandardową dla swojej aplikacji. 

## <a name="benefits-of-custom-domains"></a>Zalety domen niestandardowych

Dobrym pomysłem jest skonfigurowanie niestandardowych domen dla aplikacji, jeśli jest to możliwe. Niektóre powody używania domen niestandardowych obejmują:

- Linki między aplikacjami działają nawet poza siecią firmową. Bez domeny niestandardowej, jeśli aplikacja ma trwale kodowane linki wewnętrzne do celów poza serwerem proxy aplikacji, a linki nie są na zewnątrz rozpoznawalne, zostaną przerwane. Jeśli wewnętrzne i zewnętrzne adresy URL są takie same, można uniknąć tego problemu. Jeśli nie możesz używać domen niestandardowych, zobacz [przekierowanie stałe linki dla aplikacji opublikowanych w usłudze Azure serwer proxy aplikacji usługi Azure AD](./application-proxy-configure-hard-coded-link-translation.md) , aby poznać inne sposoby rozwiązywania tego problemu. 
  
- Użytkownicy będą mieli łatwiejszy komfort, ponieważ mogą uzyskać dostęp do aplikacji przy użyciu tego samego adresu URL z sieci lub spoza niej. Nie muszą uczyć się różnych wewnętrznych i zewnętrznych adresów URL lub śledzić ich bieżącą lokalizację. 

- Możesz kontrolować markę i utworzyć adresy URL. Domena niestandardowa może pomóc w tworzeniu pewności użytkownika, ponieważ użytkownicy widzą i używają znanej nazwy zamiast *msappproxy.NET*.

- Niektóre konfiguracje będą działały tylko z domenami niestandardowymi. Na przykład potrzebujesz domen niestandardowych dla aplikacji korzystających z SAML (SAML), na przykład podczas korzystania z Active Directory Federation Services (AD FS), ale nie można użyć usługi WS-Federation. Aby uzyskać więcej informacji, zobacz temat [współpraca z aplikacjami obsługującymi oświadczenia w serwerze proxy aplikacji](application-proxy-configure-for-claims-aware-applications.md). 

Jeśli nie masz możliwości dopasowania wewnętrznych i zewnętrznych adresów URL, nie jest tak ważne, aby można było używać domen niestandardowych, ale nadal możesz korzystać z innych korzyści. 

## <a name="dns-configuration-options"></a>Opcje konfiguracji DNS

Istnieje kilka opcji konfigurowania konfiguracji DNS, w zależności od wymagań:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Ten sam wewnętrzny i zewnętrzny adres URL, różne zachowanie wewnętrzne i zewnętrzne 

Jeśli nie chcesz, aby użytkownicy wewnętrzni korzystali z serwera proxy aplikacji, możesz skonfigurować serwer DNS z *podziałem mózgów*. Podzielona infrastruktura DNS kieruje hosty wewnętrzne do wewnętrznego serwera nazw domen i zewnętrznych hostów do zewnętrznego serwera nazw domen, na potrzeby rozpoznawania nazw. 

![Środowisko DNS z podziałem informacji](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Różne wewnętrzne i zewnętrzne adresy URL 

Jeśli wewnętrzne i zewnętrzne adresy URL różnią się od siebie, nie trzeba konfigurować zachowania podzielonego, ponieważ Routing użytkownika jest określany na podstawie adresu URL. W takim przypadku należy zmienić tylko zewnętrzny serwer DNS i skierować zewnętrzny adres URL do punktu końcowego serwera proxy aplikacji. 

Gdy wybierzesz domenę niestandardową dla zewnętrznego adresu URL, na pasku informacyjnym zostanie wyświetlony wpis CNAME, który należy dodać do zewnętrznego dostawcy DNS. Te informacje są zawsze widoczne po przejściu na stronę **serwera proxy aplikacji** .

## <a name="set-up-and-use-custom-domains"></a>Konfigurowanie i używanie domen niestandardowych

Aby skonfigurować aplikację lokalną do korzystania z domeny niestandardowej, musisz mieć zweryfikowaną Azure Active Directory domenę niestandardową, certyfikat PFX dla domeny niestandardowej oraz aplikację lokalną do skonfigurowania. 

### <a name="create-and-verify-a-custom-domain"></a>Tworzenie i Weryfikowanie domeny niestandardowej

Aby utworzyć i zweryfikować domenę niestandardową:

1. W Azure Active Directory wybierz opcję **niestandardowe nazwy domen** w lewym okienku nawigacji, a następnie wybierz pozycję **Dodaj domenę niestandardową**. 
1. Wprowadź niestandardową nazwę domeny i wybierz pozycję **Dodaj domenę**. 
1. Na stronie domena skopiuj informacje o rekordzie TXT dla Twojej domeny. 
1. Przejdź do rejestratora domen i Utwórz nowy rekord TXT dla domeny na podstawie skopiowanych informacji DNS.
1. Po zarejestrowaniu domeny na stronie domeny w Azure Active Directory wybierz pozycję **Weryfikuj**. Po **zweryfikowaniu** stanu domeny można użyć domeny we wszystkich konfiguracjach usługi Azure AD, w tym na serwerze proxy aplikacji. 

Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Dodawanie niestandardowej nazwy domeny przy użyciu portalu Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurowanie aplikacji do korzystania z domeny niestandardowej

Aby opublikować aplikację za pomocą serwera proxy aplikacji z domeną niestandardową:

1. W przypadku nowej aplikacji w obszarze Azure Active Directory wybierz pozycję **aplikacje dla przedsiębiorstw** na lewym pasku nawigacyjnym. Wybierz pozycję **Nowa aplikacja**. W sekcji **aplikacje lokalne** wybierz pozycję **Dodaj aplikację lokalną**. 
   
   W przypadku aplikacji już znajdującej się w **aplikacjach dla przedsiębiorstw** wybierz ją z listy, a następnie wybierz pozycję **serwer proxy aplikacji** w lewym okienku nawigacji. 

2. Na stronie Ustawienia serwera proxy aplikacji wprowadź **nazwę** , jeśli dodajesz własną aplikację lokalną.

3.  W polu **wewnętrzny adres URL** wprowadź wewnętrzny adres URL aplikacji.
   
4. W polu **zewnętrzny adres URL** rozwiń listę i wybierz domenę niestandardową, której chcesz użyć.
   
5. Wybierz pozycję **Dodaj**.
   
   ![Wybierz domenę niestandardową](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Jeśli domena ma już certyfikat, w polu **certyfikat** zostaną wyświetlone informacje o certyfikacie. W przeciwnym razie wybierz pole **certyfikatu** .
   
   ![Kliknij, aby przekazać certyfikat](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na stronie **certyfikat protokołu SSL** przejdź do pliku certyfikatu PFX i wybierz go. Wprowadź hasło certyfikatu i wybierz pozycję **Przekaż certyfikat**. Więcej informacji o certyfikatach znajduje się w sekcji [certyfikaty dla domen niestandardowych](#certificates-for-custom-domains) . Jeśli certyfikat jest nieprawidłowy lub wystąpił problem z hasłem, zostanie wyświetlony komunikat o błędzie. [Często zadawane pytania dotyczące serwera proxy aplikacji](application-proxy-faq.md#application-configuration) zawiera kilka kroków rozwiązywania problemów.
   
   ![Przekaż certyfikat](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Domena niestandardowa wymaga tylko jednokrotnego przekazania certyfikatu. Następnie przekazany certyfikat jest automatycznie stosowany podczas korzystania z domeny niestandardowej dla innych aplikacji.
   
8. W przypadku dodania certyfikatu na stronie **serwer proxy aplikacji** wybierz pozycję **Zapisz**. 
   
9. Na pasku informacji na stronie **serwer proxy aplikacji** Zwróć uwagę na wpis CNAME, który należy dodać do strefy DNS. 
   
   ![Dodaj wpis DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Postępuj zgodnie z instrukcjami w obszarze [Zarządzanie rekordami i zestawami rekordów DNS przy użyciu Azure Portal](../../dns/dns-operations-recordsets-portal.md) , aby dodać rekord DNS, który przekierowuje nowy zewnętrzny adres URL do domeny *msappproxy.NET* .
   
11. Aby sprawdzić, czy rekord DNS został prawidłowo skonfigurowany, użyj polecenia [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) , aby potwierdzić, że zewnętrzny adres URL jest osiągalny, a domena *msapproxy.NET* jest wyświetlana jako alias.

Aplikacja jest teraz skonfigurowana do korzystania z domeny niestandardowej. Należy pamiętać o przypisaniu użytkowników do aplikacji przed jej przetestowaniem lub wydaniem. 

Aby zmienić domenę dla aplikacji, wybierz inną domenę z listy rozwijanej w **zewnętrznym adresie URL** na stronie **serwera proxy aplikacji** aplikacji. W razie potrzeby Przekaż certyfikat dla zaktualizowanej domeny, a następnie zaktualizuj rekord DNS. Jeśli na liście rozwijanej w **zewnętrznym adresie URL** nie widzisz domeny niestandardowej, być może nie została zweryfikowana.

Aby uzyskać bardziej szczegółowe instrukcje dotyczące serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certyfikaty dla domen niestandardowych

Certyfikat tworzy bezpieczne połączenie TLS dla domeny niestandardowej. 

### <a name="certificate-formats"></a>Formaty certyfikatów

Aby upewnić się, że wszystkie wymagane certyfikaty pośrednie są uwzględniane, należy użyć certyfikatu PFX. Certyfikat musi zawierać klucz prywatny.

Większość typowych metod podpisywania certyfikatu jest obsługiwana, takich jak Alternatywna nazwa podmiotu (SAN). 

Można używać certyfikatów symboli wieloznacznych, o ile symbol wieloznaczny pasuje do zewnętrznego adresu URL. W przypadku [aplikacji wieloznacznych](application-proxy-wildcard.md)należy używać certyfikatów wieloznacznych. Aby użyć certyfikatu w celu uzyskania dostępu do domen poddomen, należy dodać symbole wieloznacznej domeny jako alternatywne nazwy podmiotu w tym samym certyfikacie. Na przykład certyfikat dla *\* . Adventure-Works.com* nie będzie działał dla elementu *\* . Apps.Adventure-Works.com* , chyba że dodasz *\* Apps.Adventure-Works.com* jako alternatywną nazwę podmiotu. 

W przypadku zainstalowania łańcucha certyfikatów na urządzeniach klienckich można użyć certyfikatów wystawionych przez własną infrastrukturę kluczy publicznych (PKI). Usługa Intune może wdrażać te certyfikaty na zarządzanych urządzeniach. W przypadku urządzeń niezarządzanych należy ręcznie zainstalować te certyfikaty. 

Nie zalecamy korzystania z prywatnego głównego urzędu certyfikacji, ponieważ należy również przeprowadzić wypychanie do komputerów klienckich prywatnego głównego urzędu certyfikacji, co może spowodować wiele wyzwań.

### <a name="certificate-management"></a>Zarządzanie certyfikatami

Wszystkie zarządzanie certyfikatami odbywa się za pomocą poszczególnych stron aplikacji. Przejdź do strony **serwer proxy aplikacji** w celu uzyskania dostępu do pola **certyfikatu** .

Po przekazaniu certyfikatu dla aplikacji zostanie ona również automatycznie zastosowana do **nowych** aplikacji skonfigurowanych do korzystania z tego samego certyfikatu. Konieczne będzie ponowne przekazanie certyfikatu dla istniejących aplikacji w dzierżawie.

Po wygaśnięciu certyfikatu zostanie wyświetlone ostrzeżenie z informacją o przekazaniu innego certyfikatu. Jeśli certyfikat zostanie odwołany, użytkownicy mogą zobaczyć ostrzeżenie o zabezpieczeniach podczas uzyskiwania dostępu do aplikacji. Aby zaktualizować certyfikat dla aplikacji, przejdź do strony **serwera proxy aplikacji** , wybierz pozycję **certyfikat** i przekaż nowy certyfikat. Jeśli stary certyfikat nie jest używany przez inne aplikacje, zostanie automatycznie usunięty. 

## <a name="next-steps"></a>Następne kroki

* [Włącz logowanie jednokrotne](application-proxy-configure-single-sign-on-with-kcd.md) do opublikowanych aplikacji przy użyciu uwierzytelniania usługi Azure AD.
* [Dostęp warunkowy](../conditional-access/concept-conditional-access-cloud-apps.md) dla opublikowanych aplikacji w chmurze.