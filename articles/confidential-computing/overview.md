---
title: Informacje poufne dotyczące platformy Azure — omówienie
description: Omówienie usług Azure CONFIDENTIAL (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: b52af8f41f32c224fd7c4fa0f5c539b23d231f49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149305"
---
# <a name="confidential-computing-on-azure"></a>Dane poufne na platformie Azure

Dane poufne platformy Azure umożliwiają izolowanie poufnych danych podczas przetwarzania w chmurze. Wiele branż wykorzystuje dane poufne do ochrony danych. Te obciążenia obejmują:

- Zabezpieczanie danych finansowych
- Ochrona informacji pacjenta
- Uruchamianie procesów uczenia maszynowego na poufnych informacjach
- Wykonywanie algorytmów na zaszyfrowanych zestawach danych z wielu źródeł


## <a name="overview"></a>Omówienie
<p><p>

> [!VIDEO https://www.youtube.com/embed/Qu6sP0XDMU8]

Wiemy, że Zabezpieczanie danych w chmurze jest ważne. Znamy Twoje problemy. Oto kilka pytań, które mogą być używane przez naszych klientów podczas przemieszczania poufnych obciążeń do chmury: 

- Jak mogę upewnij się, że firma Microsoft nie może uzyskać dostępu do danych, które nie są szyfrowane?
- Jak mogę Zapobiegaj zagrożeniom bezpieczeństwa z uprzywilejowanych administratorów w mojej firmie?
- Jakie są inne sposoby, aby uniemożliwić innym osobom dostęp do poufnych danych klienta?

Microsoft Azure pomaga zminimalizować obszar narażony na ataki w celu uzyskania silniejszej ochrony danych. Platforma Azure oferuje już wiele narzędzi do ochrony [**danych**](../security/fundamentals/encryption-atrest.md) przechowywanych przez modele, takie jak szyfrowanie po stronie klienta i szyfrowanie po stronie serwera. Ponadto platforma Azure oferuje mechanizmy szyfrowania [**danych**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) przesyłanych za pośrednictwem protokołów Secure Protocol, takich jak TLS i https. Na tej stronie wprowadzono trzeci etap szyfrowania danych — szyfrowanie **danych jest używane**.


## <a name="introduction-to-confidential-computing"></a>Wprowadzenie do danych poufnych<a id="intro to acc"></a>

Dane poufne to branżowy termin zdefiniowany przez [konsorcjum poufnego przetwarzania](https://confidentialcomputing.io/) danych (CCC), podstawą do definiowania i przyspieszania wdrażania informacji poufnych. Przetwarzanie poufne polega na ochronie danych używanych podczas wykonywania obliczeń. Obliczenia odbywają się w środowisku zaufanego wykonywania (TEE) sprzętowym.

TEE to środowisko, które wymusza wykonywanie tylko autoryzowanego kodu. Wszystkie dane w TEE nie mogą zostać odczytane ani naruszone przez żaden kod poza tym środowiskiem.

### <a name="enclaves-and-trusted-execution-environments"></a>Enclaves i zaufane środowiska wykonawcze

W kontekście obliczeń poufnych TEEs są często określane jako *enclaves* lub *Secure enclaves*. Enclaves są zabezpieczonymi częściami procesora sprzętowego i pamięci. Nie ma możliwości wyświetlania danych ani kodu w enklawy, nawet z debugerem. Jeśli niezaufany kod próbuje zmodyfikować zawartość w pamięci enklawy, środowisko zostanie wyłączone, a operacje zostaną odrzucone.

Podczas tworzenia aplikacji można używać [narzędzi programowych](#oe-sdk) do osłony fragmentów kodu i danych w enklawy. Narzędzia te zapewniają, że kod i dane nie mogą być wyświetlane ani modyfikowane przez żadną osobę spoza zaufanego środowiska. 

Zasadniczo należy traktować enklawy jako czarną ramkę. W polu należy umieścić zaszyfrowany kod i dane. Od zewnątrz pola nie są wyświetlane żadne elementy. Enklawy klucz do odszyfrowania danych, dane są następnie przetwarzane i szyfrowane ponownie przed wysłaniem z enklawy.

### <a name="attestation"></a>Zaświadczania o

Chcesz uzyskać weryfikację i sprawdzanie poprawności, czy zaufane środowisko jest bezpieczne. Ta weryfikacja jest procesem zaświadczania. 

Zaświadczanie umożliwia jednostce uzależnionej zwiększenie pewności, że ich oprogramowanie (1) działa w enklawy i (2), że enklawy jest aktualne i bezpieczne. Na przykład enklawy prosi podstawowy sprzęt do wygenerowania poświadczeń, które obejmują potwierdzenie, że enklawy istnieje na platformie. Raport może następnie zostać przekazany do drugiego enklawy, który sprawdza, czy raport został wygenerowany na tej samej platformie.

Zaświadczanie należy zaimplementować przy użyciu bezpiecznej usługi zaświadczania zgodnej z oprogramowaniem systemowym i krzemem. [Usługi zaświadczania i aprowizacji firmy Intel](https://software.intel.com/sgx/attestation-services) są zgodne z maszynami wirtualnymi do przetwarzania poufnego platformy Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Korzystanie z platformy Azure na potrzeby danych poufnych opartych na chmurze<a id="cc-on-azure"></a>

Dane poufne platformy Azure umożliwiają korzystanie z funkcji danych poufnych w środowisku zwirtualizowanym. Możesz teraz używać narzędzi, oprogramowania i infrastruktury chmurowej do kompilowania na bezpiecznym sprzęcie. 

### <a name="virtual-machines"></a>Maszyny wirtualne

Platforma Azure to pierwszy dostawca usług w chmurze, który oferuje poufne dane obliczeniowe w środowisku zwirtualizowanym. Opracowano maszyny wirtualne, które działają jako warstwa abstrakcji między sprzętem a aplikacją. Obciążenia można uruchamiać w odpowiedniej skali i z opcjami nadmiarowości i dostępności.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Virtual Machines z włączonym technologią Intel SGX

W przypadku maszyn wirtualnych z danymi poufnymi na platformie Azure część sprzętu procesora CPU jest zarezerwowana dla części kodu i danych w aplikacji. Ta część z ograniczeniami to enklawy. 

![Model maszyny wirtualnej](media/overview/hardware-backed-enclave.png)

Usługa Azure poufnej infrastruktury obliczeniowej składa się z specjalistycznej jednostki SKU maszyn wirtualnych. Te maszyny wirtualne działają na procesorach Intel przy użyciu funkcji Software Extension Guard (Intel SGX). [Procesor Intel SGX](https://intel.com/sgx) to składnik, który umożliwia zwiększonej ochrony, którą ponosi poufne dane. 

Obecnie platforma Azure oferuje [DCsv2ą](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) platformę opartą na technologii Intel SGX na potrzeby tworzenia sprzętowych enklawy. Można tworzyć bezpieczne aplikacje oparte na enklawy do uruchamiania w serii DCsv2 maszyn wirtualnych w celu ochrony danych aplikacji i używanego kodu. 

Aby dowiedzieć się [więcej](virtual-machine-solutions.md) o wdrażaniu maszyn wirtualnych do przetwarzania poufnego platformy Azure za pomocą zaufanych enclaves opartych na sprzęcie.

## <a name="application-development"></a>Opracowywanie aplikacji<a id="application-development"></a>

Aby wykorzystać możliwości enclaves i izolowanych środowisk, musisz użyć narzędzi, które obsługują dane poufne. Istnieją różne narzędzia, które obsługują Programowanie aplikacji enklawy. Można na przykład użyć tych platform typu "open source": 

- [Open enklawy Software Development Kit (SDK)](https://github.com/openenclave/openenclave)
- [Struktura konsorcjum poufnego (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Omówienie

Aplikacja skompilowana za pomocą enclaves jest partycjonowana na dwa sposoby:
1. Składnik "niezaufany" (Host)
1. Składnik "zaufany" (enklawy)

**Host** jest aplikacją enklawy działającą w niezaufanym środowisku. Kod na hoście nie może uzyskać dostępu do kodu załadowanego do enklawy. 

**Enklawy** to miejsce, w którym kod i dane są uruchamiane wewnątrz implementacji tee. Aby zapewnić ochronę wpisów tajnych i poufnych danych, należy wykonać bezpieczne obliczenia w enklawy. 

Po rozpoczęciu tworzenia aplikacji enklawy należy określić kod i dane wymagające ochrony. Kod wybrany do umieszczenia w zaufanym składniku jest odizolowany od reszty aplikacji. Po zainicjowaniu enklawy i załadowaniu kodu do pamięci kod nie może zostać odczytany lub zmieniony z zewnątrz chronionego środowiska.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Otwórz zestaw enklawy Software Development Kit (zestaw SDK programu OE)<a id="oe-sdk"></a>

Użyj biblioteki lub platformy obsługiwanej przez dostawcę, jeśli chcesz napisać kod, który jest uruchamiany w enklawy. [Open ENKLAWY SDK](https://github.com/openenclave/openenclave) (zestaw SDK programu OE) to zestaw SDK typu open source, który umożliwia abstrakcję wielu różnych sprzętowych urządzeń z obsługą informacji. 

Zestaw SDK programu OE jest oparty na jednej warstwie abstrakcji na dowolnym sprzęcie dowolnego dostawcy usług kryptograficznych. Zestaw OE SDK może być używany w odniesieniu do poufnych maszyn wirtualnych platformy Azure do tworzenia i uruchamiania aplikacji w oparciu o enclaves.

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną z serii DCsv2 i zainstaluj na niej zestaw OE.

> [!div class="nextstepaction"]
> [Wdrażanie poufnej maszyny wirtualnej w portalu Azure Marketplace](quick-create-marketplace.md)