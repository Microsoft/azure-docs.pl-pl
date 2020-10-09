---
title: Zabezpieczenia usługi Azure łańcucha bloków
description: Zagadnienia dotyczące dostępu do danych i zabezpieczeń usługi Azure łańcucha bloków
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879602"
---
# <a name="azure-blockchain-service-security"></a>Zabezpieczenia usługi Azure łańcucha bloków

Usługa Azure łańcucha bloków używa kilku funkcji platformy Azure, aby zapewnić bezpieczeństwo i dostępność danych. Dane są zabezpieczane przy użyciu izolacji, szyfrowania i uwierzytelniania.

## <a name="isolation"></a>Izolacja

Zasoby usługi Azure łańcucha bloków są odizolowane w prywatnej sieci wirtualnej. Każda transakcja i węzeł walidacji jest maszyną wirtualną (VM). Maszyny wirtualne w jednej sieci wirtualnej nie mogą komunikować się bezpośrednio z maszynami wirtualnymi w innej sieci wirtualnej. Izolacja gwarantuje, że komunikacja pozostaje prywatna w sieci wirtualnej. Aby uzyskać więcej informacji na temat izolacji sieci wirtualnej platformy Azure, zobacz [izolacja w chmurze publicznej platformy Azure](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagram sieci wirtualnej](./media/data-security/vnet.png)

## <a name="encryption"></a>Szyfrowanie

Dane użytkownika są przechowywane w usłudze Azure Storage. Dane użytkownika są szyfrowane w ruchu i są przechowywane w celu zapewnienia bezpieczeństwa i poufności. Aby uzyskać więcej informacji, zobacz: [Przewodnik po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Authentication

Transakcje można wysyłać do węzłów łańcucha bloków za pośrednictwem punktu końcowego RPC. Klienci komunikują się z węzłem transakcji przy użyciu odwrotnego serwera proxy, który obsługuje uwierzytelnianie użytkowników i szyfruje dane za pośrednictwem protokołu TLS.

![Diagram uwierzytelniania](./media/data-security/authentication.png)

Istnieją trzy tryby uwierzytelniania dostępu do usługi RPC.

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Uwierzytelnianie podstawowe używa nagłówka uwierzytelniania HTTP zawierającego nazwę użytkownika i hasło. Nazwa użytkownika jest nazwą węzła łańcucha bloków. Hasło jest ustawiane podczas aprowizacji elementu członkowskiego lub węzła. Hasło można zmienić przy użyciu Azure Portal lub interfejsu wiersza polecenia.

### <a name="access-keys"></a>Klawisze dostępu

Klucze dostępu używają losowo generowanego ciągu zawartego w adresie URL punktu końcowego. Dwa klucze dostępu umożliwiają włączenie rotacji kluczy. Klucze można generować ponownie z Azure Portal i interfejsu wiersza polecenia.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Azure Active Directory (Azure AD) korzysta z mechanizmu uwierzytelniania opartego na żądaniach, w którym użytkownik jest uwierzytelniany przez usługę Azure AD przy użyciu poświadczeń użytkownika usługi Azure AD. Usługa Azure AD zapewnia oparte na chmurze zarządzanie tożsamościami i umożliwia klientom korzystanie z jednej tożsamości w całym przedsiębiorstwie i dostęp do aplikacji w chmurze. Usługa Azure łańcucha bloków integruje się z usługą Azure AD, włączając w to Federacji identyfikatora, logowania jednokrotnego i uwierzytelniania wieloskładnikowego. W organizacji można przypisywać użytkowników, grupy i role aplikacji w celu uzyskania dostępu do elementu członkowskiego łańcucha bloków i węzła.

Serwer proxy klienta usługi Azure AD jest dostępny w witrynie [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Serwer proxy klienta kieruje użytkownika do strony logowania usługi Azure AD i uzyskuje token okaziciela po pomyślnym uwierzytelnieniu. Następnie użytkownik nawiązuje połączenie z punktem końcowym serwera proxy klienta Ethereumą aplikację kliencką, taką jak Geth lub Truffle. Na koniec po przesłaniu transakcji serwer proxy klienta wprowadza token okaziciela w nagłówku HTTP i zwrotny serwer proxy weryfikuje token przy użyciu protokołu OAuth.

## <a name="keys-and-ethereum-accounts"></a>Klucze i konta Ethereum

Podczas aprowizacji elementu członkowskiego usługi Azure łańcucha bloków jest generowana wartość konta Ethereum i klucza publicznego i prywatnego. Klucz prywatny służy do wysyłania transakcji do łańcucha bloków. Konto Ethereum to ostatnie 20 bajtów skrótu klucza publicznego. Konto Ethereum jest również nazywane portfelem.

Para kluczy prywatnych i publicznych jest przechowywana jako KeyFile w formacie JSON. Klucz prywatny jest szyfrowany przy użyciu hasła wprowadzonego podczas tworzenia usługi księgi łańcucha bloków.

Klucze prywatne są używane do cyfrowego podpisywania transakcji. W prywatnej blockchains kontrakt inteligentny podpisany przez klucz prywatny reprezentuje tożsamość osoby podpisującej. Aby sprawdzić poprawność podpisu, odbiorca może porównać klucz publiczny osoby podpisującej z adresem obliczonym na podstawie podpisu.

Klucze Constellation są używane do unikatowego identyfikowania węzła kworum. Klucze Constellation są generowane w momencie aprowizacji węzła i są określone w parametrze privateFor transakcji prywatnej w kworum.

## <a name="next-steps"></a>Następne kroki

Zobacz [jak skonfigurować Azure Active Directory dostępu do usługi Azure łańcucha bloków](configure-aad.md).
