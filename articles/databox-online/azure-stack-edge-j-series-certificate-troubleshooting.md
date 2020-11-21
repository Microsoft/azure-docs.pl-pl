---
title: Rozwiązywanie problemów z certyfikatami przy użyciu Azure Stack EDGE Pro z procesorem GPU | Microsoft Docs
description: Opisuje Rozwiązywanie problemów z błędami certyfikatów przy użyciu urządzenia z procesorem GPU w Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.author: alkohli
ms.openlocfilehash: a4848f5e8b542d80b382abe39f4882a26e0f534c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015684"
---
# <a name="troubleshooting-certificate-errors"></a>Rozwiązywanie problemów z błędami certyfikatów

Artykuł zawiera informacje dotyczące rozwiązywania typowych problemów z certyfikatami podczas instalowania certyfikatów na urządzeniu z systemem Azure Stack brzeg Pro.

## <a name="common-certificate-errors"></a>Typowe błędy certyfikatów

W poniższej tabeli przedstawiono typowe błędy certyfikatów i szczegółowe informacje dotyczące tych błędów i możliwych rozwiązań:

> [!NOTE]
> Wystąpienia &#8220;{0} , {1} ,..., {n} &#8221; wskazują parametry pozycyjne. Parametry pozycyjne będą przyjmować wartości w zależności od używanych certyfikatów.

| Kod błędu | Opis |
|---|---|
| CertificateManagement_UntrustedCertificate | Certyfikat z nazwą podmiotu {0} ma przerwany łańcuch certyfikatów. Przekaż certyfikat łańcucha podpisywania przed przekazaniem tego certyfikatu.|
| CertificateManagement_DeviceNotRegistered| Urządzenie nie zostało aktywowane. Certyfikat pomocy technicznej można przekazać dopiero po aktywacji.|
| CertificateManagement_ExpiredCertificate | Certyfikat z typem {0} wygasł lub wkrótce wygaśnie. Sprawdź ważność certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_FormatMismatch | Format certyfikatu nie jest obsługiwany. Sprawdź format certyfikatu i w razie konieczności wprowadź nowy certyfikat.  Oczekiwano {0} , znaleziono {1} . |
| CertificateManagement_GenericError | Nie można wykonać operacji zarządzania certyfikatami. Spróbuj ponownie wykonać tę operację za kilka minut. Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |
| CertificateManagement_HttpsBindingFailure | Certyfikat z nazwą podmiotu {0} nie może utworzyć bezpiecznego kanału https. Sprawdź przekazany certyfikat i w razie konieczności Przenieś nowy certyfikat. Ten błąd występuje w przypadku certyfikatu węzła urządzenia.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Certyfikat z nazwą podmiotu {0} nie powinien mieć podpisywania certyfikatu użycia klucza. Należy sprawdzić użycie klucza certyfikatu i w razie konieczności wprowadzić nowy certyfikat. Ten błąd występuje w przypadku certyfikatu łańcucha podpisywania. |
| CertificateManagement_IncorrectKeyNumber | Certyfikat z nazwą podmiotu {0} ma niepoprawny numer klucza {1} . Sprawdź numer klucza certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_InvalidP7b | Przekazany plik certyfikatu jest nieprawidłowy.  Sprawdź format certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_KeyAlgorithmNotRSA | Ten certyfikat nie używa algorytmu klucza RSA. Obsługiwane są tylko certyfikaty RSA. |
| CertificateManagement_KeySizeNotSufficient | Certyfikat z nazwą podmiotu {0} ma niewystarczający rozmiar klucza {1} . Minimalny rozmiar klucza to 4096.|
| CertificateManagement_MissingClientOid | Certyfikat z nazwą podmiotu nie {0} ma identyfikatora OID uwierzytelniania klienta. Sprawdź właściwości certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Certyfikat z nazwą podmiotu nie {0} ma podpisu cyfrowego w użyciu klucza. Sprawdź właściwości certyfikatu i w razie konieczności wprowadź nowy certyfikat. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Certyfikat z nazwą podmiotu {0} nie ma certyfikatu do podpisywania przy użyciu klucza. Sprawdź właściwości certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Certyfikat z nazwą podmiotu nie {0} ma klucza szyfrowania kluczy w użyciu klucza. Sprawdź właściwości certyfikatu i w razie konieczności wprowadź nowy certyfikat. |
| CertificateManagement_MissingServerOid | Certyfikat z nazwą podmiotu {0} nie ma identyfikatora OID uwierzytelniania serwera. Sprawdź właściwości certyfikatu i w razie konieczności wprowadź nowy certyfikat.|
| CertificateManagement_NameMismatch | Niezgodność typów certyfikatów. Oczekiwany zakres: {0} , znaleziono {1} . Przekaż odpowiedni certyfikat.|
| CertificateManagement_NoPrivateKeyPresent | Certyfikat z nazwą podmiotu nie {0} ma klucza prywatnego. Przekaż certyfikat PFX z kluczem prywatnym.|
| CertificateManagement_NoRSACryptoPrivateKey | Klucz prywatny certyfikatu z nazwą podmiotu {0} nie jest dostępny. Upewnij się, że korzystasz z obsługiwanego certyfikatu. Obsługiwany jest tylko dostawca usług kryptograficznych RSA/SChannel firmy Microsoft. |
| CertificateManagement_NotSelfSignedCertificate | Certyfikat z nazwą podmiotu {0} nie jest podpisany z podpisem własnym. Certyfikaty główne powinny być podpisane z podpisem własnym |
| CertificateManagement_NotSupportedOnVirtualAppliance | Ta operacja nie jest obsługiwana na urządzeniu wirtualnym. Ten błąd wskazuje, że podpisywanie będzie odbywać się tylko z Data Box Gateway uruchomionymi na urządzeniu z chmurą taktyczną. Ten błąd występuje podczas zarządzania urządzeniem za pomocą programu Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | Certyfikat z nazwą podmiotu {0} jest podpisany z podpisem własnym. Przekaż certyfikat, który jest poprawnie podpisany.|
| CertificateManagement_SignatureAlgorithmSha1 | Certyfikat z nazwą podmiotu {0} ma nieobsługiwany algorytm podpisu. SHA1 — RSA nie jest obsługiwane. |
| CertificateManagement_SubjectNamesInvalid | Certyfikat z nazwą podmiotu nie {0} ma poprawnej nazwy podmiotu lub alternatywnej nazwy podmiotu dla {1} certyfikatu. Sprawdź przekazany certyfikat i w razie konieczności Przenieś nowy certyfikat. Należy również sprawdzić nazwę DNS, aby dopasować nazwy sieci SAN.|
| CertificateManagement_UnreadableCertificate | {0}Nie można odczytać certyfikatu z typem. Ten błąd występuje, gdy certyfikat jest nieczytelny lub uszkodzony. Wprowadź nowy certyfikat.|
| CertificateSubjectNotFound | Nie można odnaleźć certyfikatu o nazwie podmiotu {0} . Wprowadź nowy certyfikat.|
| CertificateRotationGenericFailure | Nie można obrócić co najmniej jednego certyfikatu. Spróbuj ponownie za kilka minut. Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft.|
| CertificateImportFailure | Certyfikat z odciskiem palca {0} nie został zaimportowany w węźle {1} . Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft. |
| CertificateApplyFailure | Certyfikat z odciskiem palca {0} nie został zastosowany w węźle {1} . Jeśli problem będzie się powtarzał, skontaktuj się z pomoc techniczna firmy Microsoft.|
| NodeNotReachable | Nie można zweryfikować certyfikatu {0} . Sprawdź kondycję sprzętu i oprogramowania systemu.|


## <a name="next-steps"></a>Następne kroki

[Wymagania dotyczące certyfikatów](azure-stack-edge-j-series-certificate-requirements.md)