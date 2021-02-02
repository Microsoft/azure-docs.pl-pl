---
title: aplikacja usługi Azure AD serwera proxy i wykrywania Qlik | Microsoft Docs
description: Włącz serwer proxy aplikacji w Azure Portal i zainstaluj łączniki dla zwrotnego serwera proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259088"
---
# <a name="application-proxy-and-qlik-sense"></a>Serwer proxy aplikacji i wykrywanie Qlik 
Serwer proxy aplikacji usługi Azure Active Directory i Qlik ze sobą współpracowały, aby zapewnić możliwość korzystania z serwera proxy aplikacji w celu zapewnienia dostępu zdalnego do wdrożenia Qlik.  

## <a name="prerequisites"></a>Wymagania wstępne 
W pozostałej części tego scenariusza założono, że wykonano następujące czynności:
 
- Skonfigurowany [Qlik](https://community.qlik.com/docs/DOC-19822). 
- [Zainstalowano łącznik serwera proxy aplikacji](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikowanie aplikacji na platformie Azure 
Aby opublikować QlikSense, należy opublikować dwie aplikacje na platformie Azure.  

### <a name="application-1"></a>#1 aplikacji: 
Wykonaj następujące kroki, aby opublikować aplikację. Aby zapoznać się z bardziej szczegółowym omówieniem kroków 1-8, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 


1. Zaloguj się w witrynie Azure Portal jako administrator globalny. 
2. Wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. 
3. Wybierz pozycję **Dodaj** w górnej części bloku. 
4. Wybierz **aplikację lokalną**. 
5. Wypełnij pola wymagane informacjami o nowej aplikacji. Skorzystaj z następujących wskazówek dotyczących ustawień: 
   - **Wewnętrzny adres URL**: Ta aplikacja powinna mieć wewnętrzny adres URL, który jest samym adresem URL QlikSense. Na przykład **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda wstępnego uwierzytelniania**: Azure Active Directory (zalecane, ale nie wymagane) 
1. Wybierz pozycję **Dodaj** w dolnej części bloku. Aplikacja zostanie dodana i zostanie otwarte menu szybkiego startu. 
2. W menu szybki Start wybierz pozycję **Przypisz użytkownika do testowania** i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej. 
3. Wybierz pozycję **Przypisz** , aby zapisać testowe przypisanie użytkownika. 
4. Obowiązkowe W bloku Zarządzanie aplikacjami wybierz pozycję Logowanie jednokrotne. Wybierz **ograniczone delegowanie protokołu Kerberos** z menu rozwijanego i Wypełnij wymagane pola w oparciu o konfigurację Qlik. Wybierz pozycję **Zapisz**. 

### <a name="application-2"></a>#2 aplikacji: 
Wykonaj te same czynności co w przypadku aplikacji #1 z następującymi wyjątkami: 

**Krok #5**: wewnętrzny adres URL powinien teraz być adresem URL QlikSense z portem uwierzytelniania używanym przez aplikację. Wartość domyślna to **4244** dla protokołów HTTPS i **4248** dla protokołu HTTP dla wersji QlikSense wcześniejszych niż kwiecień 2018. Domyślne wersje QlikSense po kwietnia 2018 to **443** dla protokołów HTTPS i **80** dla protokołu HTTP.  Np.: **https&#58;//demo.Qlik.com:4244**</br></br>
**#10 krok:** Nie Konfiguruj logowania jednokrotnego i pozostaw **Logowanie** jednokrotne wyłączone
 
 
## <a name="testing"></a>Testowanie 
Aplikacja jest teraz gotowa do przetestowania. Uzyskaj dostęp do zewnętrznego adresu URL, który został użyty do opublikowania QlikSense w aplikacji #1 i zaloguj się jako użytkownik przypisany do obu aplikacji.  

## <a name="additional-references"></a>Dodatkowa dokumentacja
Aby uzyskać więcej informacji na temat publikowania Qlik z serwerem proxy aplikacji, zapoznaj się z następującymi artykułami społeczności Qlik: 
- [Usługa Azure AD z zintegrowanym uwierzytelnianiem systemu Windows przy użyciu ograniczonego delegowania protokołu Kerberos z Qlik sensem](https://community.qlik.com/docs/DOC-20183)
- [Integracja Qlik z platformą Azure serwer proxy aplikacji usługi Azure AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Praca z łącznikami serwera proxy aplikacji](application-proxy-connector-groups.md)

