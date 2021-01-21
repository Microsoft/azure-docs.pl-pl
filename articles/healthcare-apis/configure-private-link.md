---
title: Prywatny link do usługi Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania prywatnego punktu końcowego dla interfejsu API platformy Azure dla usług FHIR Services
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: cdb41f12e8f050e3c74fccddb392d7a816c15b2f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621882"
---
# <a name="configure-private-link"></a>Konfigurowanie linku prywatnego

Link prywatny umożliwia dostęp do usługi Azure API for FHIR za pośrednictwem prywatnego punktu końcowego, interfejsu sieciowego, który nawiązuje połączenie prywatnie i bezpiecznie przy użyciu prywatnego adresu IP z sieci wirtualnej. Za pomocą linku prywatnego możesz bezpiecznie uzyskiwać dostęp do naszych usług z sieci wirtualnej jako usługi pierwszej firmy bez konieczności przechodzenia przez publiczny system DNS. W tym artykule omówiono sposób tworzenia, testowania i zarządzania prywatnym punktem końcowym usługi Azure API for FHIR.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem prywatnego punktu końcowego należy najpierw utworzyć zasoby platformy Azure:

- Grupa zasobów — Grupa zasobów platformy Azure, która będzie zawierać sieć wirtualną i prywatny punkt końcowy.
- Azure API for FHIR — zasób FHIR, który chcesz umieścić za prywatnym punktem końcowym.
- Virtual Network — sieć wirtualna, z którą będą połączone usługi klienta i prywatny punkt końcowy.

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją prywatnego linku](../private-link/index.yml).

## <a name="disable-public-network-access"></a>Wyłącz dostęp do sieci publicznej

Utworzenie prywatnego punktu końcowego dla zasobu FHIR nie powoduje automatycznego wyłączenia ruchu publicznego. Aby to zrobić, należy zaktualizować zasób FHIR, aby ustawić nową właściwość "Public Access" z "Enabled" na "Disabled". Należy zachować ostrożność podczas wyłączania dostępu do sieci publicznej jako wszystkie żądania do usługi FHIR, które nie pochodzą z prawidłowo skonfigurowanego prywatnego punktu końcowego. Dozwolony jest tylko ruch z prywatnych punktów końcowych.

![Wyłącz dostęp do sieci publicznej](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

Aby utworzyć prywatny punkt końcowy, deweloper z uprawnieniami RBAC w ramach zasobu FHIR może używać Azure Portal, [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md). W tym artykule omówiono procedurę korzystania z Azure Portal. Używanie Azure Portal jest zalecane, ponieważ automatyzuje tworzenie i konfigurację strefy Prywatna strefa DNS. Aby uzyskać więcej informacji, możesz odwoływać się do [prywatnego linku szybki start przewodników](../private-link/create-private-endpoint-portal.md) .

Istnieją dwa sposoby tworzenia prywatnego punktu końcowego. Przepływ autozatwierdzania umożliwia użytkownikowi, który ma uprawnienia RBAC w zasobie FHIR, tworzenie prywatnego punktu końcowego bez konieczności zatwierdzania. Przepływ ręcznego zatwierdzania umożliwia użytkownikowi bez uprawnień do zasobu FHIR zażądanie prywatnego punktu końcowego, który ma zostać zatwierdzony przez właścicieli zasobu FHIR.

### <a name="auto-approval"></a>Autozatwierdzanie

Upewnij się, że region nowego prywatnego punktu końcowego jest taki sam jak region Virtual Network. Region zasobu FHIR może być różny.

![Karta podstawy Azure Portal](media/private-link/private-link-portal2.png)

W obszarze Typ zasobu Wyszukaj i wybierz pozycję "Microsoft. HealthcareApis/Services". W obszarze zasób wybierz zasób FHIR. Dla docelowego zasobu podrzędnego wybierz pozycję "fhir".

![Karta zasobów Azure Portal](media/private-link/private-link-portal1.png)

Jeśli nie masz skonfigurowanej strefy Prywatna strefa DNS, wybierz pozycję "(New) privatelink. azurehealthcareapis. com". Jeśli masz już skonfigurowaną strefę Prywatna strefa DNS, możesz wybrać ją z listy. Musi mieć format "privatelink.azurehealthcareapis.com".

![Karta Konfiguracja Azure Portal](media/private-link/private-link-portal3.png)

Po zakończeniu wdrażania możesz wrócić do karty "połączenia prywatne punktów końcowych", na której będzie wyświetlany stan połączenia "zatwierdzone".

### <a name="manual-approval"></a>Zatwierdzenie ręczne

W przypadku zatwierdzania ręcznego wybierz drugą opcję w obszarze zasób, "Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu". W przypadku docelowego zasobu podrzędnego wprowadź wartość "fhir" jako opcję autozatwierdzanie.

![Zatwierdzenie ręczne](media/private-link/private-link-manual.png)

Po zakończeniu wdrażania możesz wrócić do karty "połączenia prywatne punktów końcowych", na której można zatwierdzić, odrzucić lub usunąć połączenie.

![Opcje](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Testuj prywatny punkt końcowy

Aby upewnić się, że serwer FHIR nie otrzymuje ruchu publicznego po wyłączeniu dostępu do sieci publicznej, spróbuj przejść do punktu końcowego/Metadata serwera z komputera. Powinna zostać wyświetlona 403 zabronione. Należy pamiętać, że po aktualizacji flagi dostępu do sieci publicznej może upłynąć do 5 minut od momentu zablokowania ruchu publicznego.

Aby upewnić się, że prywatny punkt końcowy może wysyłać ruch do serwera:

1. Utwórz maszynę wirtualną, która jest połączona z siecią wirtualną i podsieć, w której jest skonfigurowany prywatny punkt końcowy. Aby zapewnić, że ruch z maszyny wirtualnej odbywa się tylko przy użyciu sieci prywatnej, można wyłączyć wychodzący ruch internetowy za pośrednictwem reguły sieciowej grupy zabezpieczeń.
2. RDP z maszyną wirtualną.
3. Spróbuj użyć punktu końcowego/Metadata serwera FHIR na maszynie wirtualnej, aby otrzymać instrukcję możliwości jako odpowiedź.

## <a name="manage-private-endpoint"></a>Zarządzanie prywatnym punktem końcowym

### <a name="view"></a>Widok

Prywatne punkty końcowe i skojarzona z nią karty sieciowe są widoczne w Azure Portal z grupy zasobów, w której zostały utworzone.

![Wyświetl w zasobach](media/private-link/private-link-view.png)

### <a name="delete"></a>Usuń

Prywatne punkty końcowe można usunąć tylko z Azure Portal za pośrednictwem bloku przegląd (jak poniżej) lub za pośrednictwem opcji Usuń w obszarze Sieć (wersja zapoznawcza) karta "połączenia prywatne punktów końcowych". Kliknięcie przycisku Usuń spowoduje usunięcie prywatnego punktu końcowego i skojarzonej karty sieciowej. Jeśli usuniesz wszystkie prywatne punkty końcowe do zasobu FHIR i dostęp do sieci publicznej zostanie wyłączony, żądanie nie zostanie wprowadzone do serwera FHIR.

![Usuń prywatny punkt końcowy](media/private-link/private-link-delete.png)
