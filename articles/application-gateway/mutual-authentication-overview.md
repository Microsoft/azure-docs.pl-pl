---
title: Przegląd wzajemnego uwierzytelniania na platformie Azure Application Gateway
description: Ten artykuł zawiera omówienie uwierzytelniania wzajemnego na Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231528"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Przegląd wzajemnego uwierzytelniania za pomocą Application Gateway (wersja zapoznawcza)

Uwierzytelnianie wzajemne lub uwierzytelnianie klienta umożliwia Application Gateway uwierzytelniania żądań wysyłanych przez klientów. Zwykle tylko klient uwierzytelnia Application Gateway; wzajemne uwierzytelnianie umożliwia zarówno klientowi, jak i Application Gateway wzajemne uwierzytelnianie. 

> [!NOTE]
> Zalecamy korzystanie z protokołu TLS 1,2 z uwierzytelnianiem obustronnym, ponieważ protokół TLS 1,2 będzie w przyszłości uznawany za. 

## <a name="mutual-authentication"></a>Uwierzytelnianie wzajemne

Application Gateway obsługuje wzajemne uwierzytelnianie oparte na certyfikatach, w którym można przekazać zaufane certyfikaty urzędu certyfikacji klienta do Application Gateway, a Brama będzie używać tego certyfikatu do uwierzytelniania klienta wysyłającego żądanie do bramy. Ze wzrostem przypadków użycia IoT i zwiększonymi wymaganiami dotyczącymi zabezpieczeń w różnych branżach, wzajemne uwierzytelnianie zapewnia sposób zarządzania i kontrolowania klientów, którzy mogą komunikować się z Application Gateway. 

Aby można było skonfigurować uwierzytelnianie wzajemne, Certyfikat zaufanego urzędu certyfikacji musi zostać przekazany jako część części uwierzytelnianie klienta w profilu SSL. Aby można było ukończyć konfigurację wzajemnego uwierzytelniania, należy najpierw skojarzyć profil SSL z odbiornikiem. W przekazywanym certyfikacie klienta musi być zawsze certyfikat głównego urzędu certyfikacji. Istnieje również możliwość przekazania łańcucha certyfikatów, ale łańcuch musi zawierać certyfikat głównego urzędu certyfikacji oprócz wielu pośrednich certyfikatów urzędu certyfikacji. 

Na przykład jeśli certyfikat klienta zawiera certyfikat głównego urzędu certyfikacji, wiele certyfikatów pośrednich urzędów certyfikacji i certyfikat liścia, upewnij się, że certyfikat głównego urzędu certyfikacji i wszystkie certyfikaty pośrednich urzędów certyfikacji są przekazywane do Application Gateway w jednym pliku. Aby uzyskać więcej informacji na temat wyodrębniania certyfikatu zaufanego klienta urzędu certyfikacji, zobacz [Jak wyodrębnić certyfikaty zaufanych klientów urzędu certyfikacji](./mutual-authentication-certificate-management.md).

Jeśli przekazujesz łańcuch certyfikatów z głównym urzędem certyfikacji i pośrednimi certyfikatami urzędu certyfikacji, łańcuch certyfikatów musi zostać przekazany jako plik PEM lub CER do bramy. 

> [!NOTE] 
> Uwierzytelnianie wzajemne jest dostępne tylko dla Standard_v2 i WAF_v2 jednostek SKU. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certyfikaty obsługiwane na potrzeby wzajemnego uwierzytelniania

Application Gateway obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji (CA)
- Certyfikaty urzędu certyfikacji z podpisem własnym: przeglądarki klienta nie ufają tym certyfikatom i ostrzegają użytkownika o tym, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty urzędu certyfikacji z podpisem własnym są przydatne w przypadku testowania lub środowisk, w których Administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny używać certyfikatów urzędu certyfikacji z podpisem własnym.

Aby uzyskać więcej informacji na temat konfigurowania wzajemnego uwierzytelniania, zobacz [Konfigurowanie wzajemnego uwierzytelniania za pomocą Application Gateway](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Upewnij się, że podczas korzystania z uwierzytelniania wzajemnego przekazano cały łańcuch certyfikatów zaufanego urzędu certyfikacji klienta do Application Gateway. 

## <a name="additional-client-authentication-validation"></a>Dodatkowa weryfikacja uwierzytelniania klienta

### <a name="verify-client-certificate-dn"></a>Sprawdź nazwę wyróżniającą certyfikatu klienta

Istnieje możliwość zweryfikowania bezpośredniego wystawcy certyfikatu klienta i zezwolenia na Application Gateway zaufania tego wystawcy. Ta opcja jest domyślnie wyłączona, ale można ją włączyć za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. 

Jeśli zdecydujesz się włączyć Application Gateway, aby sprawdzić, czy certyfikat klienta zostanie wystawiony przez wydawcę, poniżej przedstawiono sposób ustalania, jak nazwa DN wystawcy certyfikatu klienta zostanie wyodrębniona z przekazanych certyfikatów. 
* **Scenariusz 1:** Łańcuch certyfikatów obejmuje: certyfikat główny-pośredni certyfikat typu liść 
    * Nazwa podmiotu *certyfikatu pośredniego* to Application Gateway, który zostanie wyodrębniony jako nazwa wyróżniająca wystawcy certyfikatu klienta i zostanie zweryfikowany. 
* **Scenariusz 2:** Łańcuch certyfikatów obejmuje: certyfikat główny-intermediate1 certyfikat-intermediate2 certyfikat-liść
    * Nazwa podmiotu *certyfikatu Intermediate2* będzie wyodrębniana jako DN certyfikatu klienta i zostanie zweryfikowana. 
* **Scenariusz 3:** Łańcuch certyfikatów obejmuje: certyfikat główny-liść certyfikatu 
    * Nazwa podmiotu *certyfikatu głównego* zostanie wyekstrahowana i użyta jako nazwa wyróżniająca wystawcy certyfikatu klienta.
* **Scenariusz 4:** Wiele łańcuchów certyfikatów o tej samej długości w tym samym pliku. Łańcuch 1 obejmuje: certyfikat główny-intermediate1 certyfikat typu liść. Łańcuch 2 zawiera: certyfikat główny-intermediate2 certyfikat typu liść. 
    * Nazwa podmiotu *certyfikatu Intermediate1* zostanie wyodrębniona jako DN wydawcy certyfikatu klienta.  
* **Scenariusz 5:** Wiele łańcuchów certyfikatów o różnych długościach w tym samym pliku. Łańcuch 1 obejmuje: certyfikat główny-intermediate1 certyfikat typu liść. Łańcuch 2 zawiera certyfikat główny intermediate2 certyfikatu-intermediate3 certyfikat typu liść. 
    * Nazwa podmiotu *certyfikatu Intermediate3* zostanie wyodrębniona jako DN wydawcy certyfikatu klienta. 

> [!IMPORTANT]
> Zalecamy przekazywanie tylko jednego łańcucha certyfikatów dla każdego pliku. Jest to szczególnie ważne w przypadku włączenia Sprawdź nazwę DN certyfikatu klienta. Przekazując wiele łańcuchy certyfikatów w jednym pliku, będzie można zakończyć w scenariuszu czwartym lub pięciu i może wystąpić w dalszej części tego problemu, gdy przedstawiony certyfikat klienta nie jest zgodny z nazwą wyróżniającą wystawcy certyfikatu klienta Application Gateway wyodrębnionym z łańcuchów. 

Aby uzyskać więcej informacji na temat wyodrębniania zaufanych łańcuchów certyfikatów klienta urzędu certyfikacji, zobacz [Jak wyodrębnić łańcuch certyfikatów zaufanego urzędu certyfikacji klienta](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Zmienne serwera 

W przypadku wzajemnego uwierzytelniania istnieją dodatkowe zmienne serwera, których można użyć do przekazania informacji o certyfikacie klienta do serwerów zaplecza za Application Gateway. Aby uzyskać więcej informacji o tym, które zmienne serwera są dostępne i jak ich używać, sprawdź [zmienne serwera](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z uwierzytelnianiem obustronnym przejdź do pozycji [konfigurowanie Application Gateway z uwierzytelnianiem obustronnym w programie PowerShell](./mutual-authentication-powershell.md) , aby utworzyć Application Gateway przy użyciu wzajemnego uwierzytelniania. 

