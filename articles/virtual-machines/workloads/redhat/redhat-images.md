---
title: Omówienie Red Hat Enterprise Linux obrazów na platformie Azure
description: Dowiedz się więcej na temat Red Hat Enterprise Linux obrazów w Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 03c033ac5d0f20bbfd4f5dccfb5c2900596d2a41
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673125"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Przegląd obrazów Red Hat Enterprise Linux

W tym artykule opisano dostępne obrazy Red Hat Enterprise Linux (RHEL) w witrynie Azure Marketplace oraz zasady dotyczące ich nazewnictwa i przechowywania.

Aby uzyskać informacje na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata). Aby uzyskać szczegółowe informacje o cenach, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Obecnie dostępne są obrazy RHEL w portalu Azure Marketplace — Obsługa własnych subskrypcji (BYOS) lub modeli licencjonowania z opcją płatność zgodnie z rzeczywistym użyciem. Dynamiczne przełączanie między BYOS i licencjonowaniem zgodnie z rzeczywistym użyciem można przeprowadzić za pomocą korzyści z [używania hybrydowej platformy Azure](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> W przypadku dowolnego problemu związanego z obrazami RHEL w portalu Azure Marketplace należy uzyskać bilet pomocy technicznej w firmie Microsoft.

## <a name="view-images-available-in-azure"></a>Wyświetlanie obrazów dostępnych na platformie Azure

Podczas wyszukiwania "Red Hat" w witrynie Azure Marketplace lub podczas tworzenia zasobu w interfejsie użytkownika Azure Portal zobaczysz tylko podzestaw wszystkich dostępnych obrazów RHEL. Pełny zestaw dostępnych obrazów maszyn wirtualnych można zawsze uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell i interfejsu API.

Aby wyświetlić pełny zestaw dostępnych obrazów Red Hat na platformie Azure, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konwencja nazewnictwa

Obrazy maszyn wirtualnych na platformie Azure są zorganizowane według wydawcy, oferty, jednostki SKU i wersji. Kombinacja wydawcy: Oferta: SKU: wersja to obraz URN i unikatowa identyfikacja obrazu do użycia.

Na przykład `RedHat:RHEL:8-LVM:8.1.20200318` odnosi się do obrazu RHEL 8,1 LVM, utworzonego z 18 marca 2020.

Poniżej przedstawiono przykład sposobu tworzenia maszyny wirtualnej z systemem RHEL 8,1.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Krótka moniker

Interfejs API REST platformy Azure umożliwia korzystanie z monikera "Najnowsza" w wersji zamiast określonej wersji. Użycie "Najnowsze" stanowi najnowszy dostępny obraz dla danego wydawcy, oferty i jednostki SKU.

Na przykład `RedHat:RHEL:8-LVM:latest` odwołuje się do najnowszej RHEL 8 rodzina LVM.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Ogólnie porównanie wersji do określenia najnowszych jest zgodne z regułami [metody CompareTo](/dotnet/api/system.version.compareto#system_version_compareto_system_version_).
To porównanie wersji obrazu jest wykonywane przez porównanie wartości jako obiektu [wersji](/dotnet/api/system.version.-ctor) , a nie ciągu.

## <a name="rhel-6-image-types"></a>RHEL 6 — typy obrazów

>[!NOTE]
> Od grudnia 30 2020 RHEL 6,10 wszedł w życie (EOL). Aby uzyskać dalszą pomoc techniczną, Włącz ELS w ramach fazy rozszerzonej pomocy technicznej cyklu życia, zobacz [dokumentację Red Hat Els](./redhat-extended-lifecycle-support.md).

W przypadku obrazów RHEL 6. x typy obrazów przedstawiono w poniższej tabeli.

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (na przykład 6,9) | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 6.9.2018010506) | Wszystkie obrazy standardowego RHEL 6. x są zgodne z tą konwencją.
|RedHat | RHEL — BYOS | RHEL — raw69 | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 6.9.20181023) | Ten obraz jest obrazem RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL — SAP-APPS | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 6.8.2017053118) | Ten obraz to obraz RHEL 6,8 for SAP Applications. Jest on uprawniony do uzyskiwania dostępu do repozytoriów aplikacji SAP i podstawowych repozytoriów RHEL.
|RedHat | RHEL | RHEL — SAP-HANA | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 6.7.2017053121) | Ten obraz to RHEL 6,7 dla obrazu SAP HANA. Jest on uprawniony do uzyskiwania dostępu SAP HANA repozytoriów i podstawowych repozytoriów RHEL.

## <a name="rhel-7-image-types"></a>RHEL 7 — typy obrazów

W przypadku obrazów RHEL 7. x istnieje kilka różnych typów obrazów. W poniższej tabeli przedstawiono różne zestawy oferowanych przez nas obrazów. Aby wyświetlić pełną listę, użyj interfejsu wiersza polecenia platformy Azure `az vm image list --publisher redhat --all` .

>[!NOTE]
> O ile nie wskazano inaczej, wszystkie obrazy są LVM partycjonowane i łączą się z regularnymi repozytoriami RHEL. Oznacza to, że repozytoria nie są rozszerzoną obsługą aktualizacji (EUS) i nie są usługami Update Services dla oprogramowania SAP (E4S). Przechodząc do przodu, przeniesiemy do publikowania tylko obrazów z LVMą, ale są otwarte do przesyłania opinii na temat tej decyzji. Aby uzyskać więcej informacji na temat rozszerzonej obsługi aktualizacji i usług aktualizacji dla oprogramowania SAP, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (na przykład 7,6) | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019102813) | Obrazy opublikowane przed 2019 kwietnia są dołączone do standardowych repozytoriów RHEL. Obrazy opublikowane po 2019 kwietnia są dołączone do repozytoriów EUS firmy Red Hat w celu umożliwienia blokowania wersji określonej wersji pomocniczej. Klienci, którzy chcą regularnie repozytoriów, powinni używać obrazów zawierających 7-LVM lub 7-RAW w wartości SKU (szczegóły poniżej). Obrazy RHEL 7,7 i nowsze są LVM podzielone na partycje. Wszystkie inne obrazy w tej kategorii są niesformatowane.
|RedHat | RHEL | 7 — NIEPRZETWORZONY | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019102813) | Obrazy te są nieprzetworzone na partycje (na przykład żadne woluminy logiczne nie zostały dodane).
|RedHat | RHEL | 7-RAW-CI | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019072418) | Obrazy te są nieprzetworzone na partycje (na przykład żadne woluminy logiczne nie zostały dodane) i nie można zainicjować obsługi administracyjnej przy użyciu funkcji Cloud-init.
|RedHat | RHEL | 7 — LVM | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019062414) | Te obrazy są LVM podzielone na partycje.
|RedHat | RHEL — BYOS | RHEL — {LVM, RAW} | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.7.20190819) | Te obrazy to obrazy w programie RHEL 7 BYOS. Nie są one dołączone do żadnych repozytoriów i nie pobierają opłaty za usługę RHEL Premium. Jeśli interesują Cię obrazy RHEL BYOS, [Zażądaj dostępu](https://aka.ms/rhel-byos). Wartości jednostki SKU kończą się wersją pomocniczą i oznaczają, czy obraz jest sformatowany jako RAW, czy LVM. Na przykład wartość SKU elementu RHEL-lvm77 wskazuje obraz RHEL 7,7 z partycją LVM.
|RedHat | RHEL | RHEL — SAP | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019071300) | Obrazy te są RHEL dla obrazów SAP. Są uprawnieni do uzyskiwania dostępu do repozytoriów SAP HANA i aplikacji, a także repozytoriów E4S RHEL. Rozliczenia obejmują RHEL Premium i SAP Premium na podstawie podstawowej opłaty za obliczenia.
|RedHat | RHEL | RHEL — SAP-HA | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019062320) | Te obrazy są RHEL dla oprogramowania SAP z obrazami wysokiej dostępności i aktualizacji usług. Są one uprawnione do uzyskiwania dostępu do repozytoriów SAP HANA i aplikacji oraz repozytoriów o wysokiej dostępności, a także repozytoriów E4S RHEL. Rozliczenia obejmują usługę RHEL Premium, SAP Premium i High Availability Premium na podstawie podstawowej opłaty za obliczenia.
|RedHat | RHEL | RHEL – HA | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.6.2019062019) | Te obrazy to obrazy RHEL, które są również uprawnione do uzyskiwania dostępu do dodatku o wysokiej dostępności. Opłaty są naliczane nieznacznie w oparciu o RHEL i podstawowe opłaty za obliczenia związane z wysoką dostępnością.
|RedHat | RHEL | RHEL — SAP-APPS | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.3.2017053118) | Te obrazy są nieaktualne, ponieważ aplikacje SAP i repozytoria SAP HANA zostały połączone z repozytoriami SAP. Obrazy te są RHEL dla obrazów aplikacji SAP. Są one uprawnione do uzyskiwania dostępu do repozytoriów aplikacji SAP i podstawowych repozytoriów RHEL.
|RedHat | RHEL | RHEL — SAP-HANA | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 7.3.2018051421) | Te obrazy są nieaktualne, ponieważ aplikacje SAP i repozytoria SAP HANA zostały połączone z repozytoriami SAP. Obrazy te są RHEL for SAP HANA obrazy. Są one uprawnione do uzyskiwania dostępu SAP HANA repozytoriów i podstawowych repozytoriów RHEL.

## <a name="rhel-8-image-types"></a>RHEL 8 — typy obrazów

>[!NOTE]
> Red Hat zaleca użycie Grubby do konfigurowania parametrów wiersza polecenia jądra w RHEL 8 +. Więcej szczegółów można znaleźć [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Szczegóły dotyczące typów obrazów RHEL 8 są poniżej.

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 8.0.20191023) | Są to obrazy z RHEL 8 LVM na partycje połączone z standardowymi repozytoriami Red Hat.
|RedHat | RHEL | 8 — Gen2 | Połączone wartości RHEL wersja pomocnicza i Data opublikowania (na przykład 8.0.20191024) | Są to obrazy z funkcją Hyper-V Generation 2 RHEL 8 LVM z obrazami podłączonymi do standardowych repozytoriów Red Hat. Aby uzyskać więcej informacji na temat maszyn wirtualnych 2. generacji na platformie Azure, zobacz [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../../generation-2.md).

## <a name="rhel-extended-support-add-ons"></a>Dodatki obsługi rozszerzonej RHEL

### <a name="extended-life-cycle-support"></a>Rozszerzona obsługa cyklu życiowego

Dodatek z rozszerzonym cyklem życia (ELS) jest opcjonalną subskrypcją, która umożliwia wykonywanie krytycznych i ważnych poprawek zabezpieczeń dla wydań, które osiągnęły koniec cyklu życia (EOL). Więcej informacji na temat RHEL EUS jest dostępnych w [dokumentacji Red Hat](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support).

ELS jest obecnie dostępna tylko dla RHEL 6,10. W przypadku obrazów z opcją płatność zgodnie z rzeczywistym użyciem ELS można włączyć, wykonując kroki opisane w [dokumentacji Red Hat Els](./redhat-extended-lifecycle-support.md).

Jeśli korzystasz ze starszej wersji, przed włączeniem ELS należy uaktualnić do RHEL 6,10.

### <a name="extended-update-support"></a>Rozszerzona obsługa aktualizacji

Od kwietnia 2019 obrazy RHEL są dostępne, które są domyślnie dołączone do repozytoriów EUS. Więcej informacji na temat RHEL EUS jest dostępnych w [dokumentacji Red Hat](https://access.redhat.com/articles/rhel-eus).

Możliwe jest przełączenie do repozytoriów EUS i jest ono obsługiwane. Aby uzyskać instrukcje dotyczące sposobu przełączenia maszyny wirtualnej do EUS i więcej informacji na temat EUS pomoc techniczną, zobacz [RHEL EUS i blokowanie wersji RHEL maszyny wirtualne](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS nie są obsługiwane w dodatkach RHEL. Oznacza to, że w przypadku instalowania pakietu, który jest zazwyczaj dostępny w kanale RHEL Extras, nie będzie można wykonać tego działania w EUS. Aby uzyskać więcej informacji na temat cyklu życia produktu Red Hat, zobacz [Red Hat Enterprise Linux cykl życia usługi Extras](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Rozróżnianie obrazów zwykłych i EUS

Klienci, którzy chcą korzystać z obrazów dołączonych do repozytoriów EUS, powinni używać obrazu RHEL zawierającego numer pomocniczej RHEL w jednostce SKU.

Na przykład mogą zostać wyświetlone następujące dwa dostępne obrazy z RHEL 7,4.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

W tym przypadku `RedHat:RHEL:7.6:7.6.2019102813` jest domyślnie dołączony do repozytoriów EUS. Wartość SKU to 7,4. I `RedHat:RHEL:7-LVM:7.6.2019062414` jest domyślnie dołączone do repozytoriów innych niż EUS. Wartość SKU to 7-LVM.

Aby używać zwykłych repozytoriów (innych niż EUS), należy użyć obrazu, który nie zawiera pomocniczego numeru wersji w jednostce SKU.

#### <a name="rhel-images-with-eus"></a>Obrazy RHEL z EUS

Informacje przedstawione w poniższej tabeli dotyczą obrazów RHEL, które są połączone z repozytoriami EUS.

>[!NOTE]
> W czasie pisania tylko RHEL 7,4 i nowsze wersje pomocnicze mają EUS pomoc techniczną. EUS nie jest już obsługiwana dla RHEL <= 7,3.
>
> Aby uzyskać więcej informacji na temat dostępności usługi RHEL EUS, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).

Wersja pomocnicza |Przykład obrazu EUS              |Stan EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Obrazy opublikowane w kwietniu 2019 i nowszych są domyślnie EUS.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Obrazy opublikowane czerwiec 2019 i nowsze są domyślnie EUS. |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Obrazy opublikowane z maja 2019 i nowsze są domyślnie EUS. |
RHEL 8,0      |Nie dotyczy                            | W Red Hat nie jest dostępny żaden EUS.                               |

### <a name="update-services-for-sap"></a>Aktualizowanie usług dla oprogramowania SAP

Najnowsze RHEL obrazów SAP zostaną połączone z usługami Update Services for SAP Solutions subscriptions (E4S). Aby uzyskać więcej informacji na temat E4S, zobacz [dokumentację](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)Red Hat.

#### <a name="rhel-images-with-e4s"></a>Obrazy RHEL z E4S

Obrazy z następujących ofert utworzonych po grudniu 2019 są połączone z repozytoriami E4S:

* RHEL — SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL for SAP with High Availability i Update Services)

## <a name="other-available-offers-and-skus"></a>Inne dostępne oferty i jednostki SKU

Pełna lista dostępnych ofert i jednostek SKU może zawierać dodatkowe obrazy poza wymienionymi w poprzedniej tabeli. Może to być na przykład `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Te oferty mogą służyć do zapewnienia pomocy technicznej dla określonych rozwiązań w portalu Marketplace. Lub można je opublikować w celach podglądu i testowania. Mogą zostać zmienione lub usunięte w dowolnym momencie bez ostrzeżenia. Nie używaj ich, chyba że ich obecność jest publicznie udokumentowana przez firmę Microsoft lub Red Hat.

## <a name="publishing-policy"></a>Zasady publikowania

Obrazy aktualizacji firmy Microsoft i Red Hat jako nowe wersje pomocnicze są udostępniane, zgodnie z potrzebami, w celu rozwiązania określonych typowych luk w zabezpieczeniach i zagrożeń (CVEs) lub w przypadku okazjonalnych zmian konfiguracji lub aktualizacji. Dążymy do udostępnienia zaktualizowanych obrazów najszybciej, jak to możliwe, w ciągu trzech dni roboczych po wydaniu lub udostępnieniu poprawki CVE.

Aktualizujemy tylko bieżącą wersję pomocniczą w danej rodzinie obrazu. W przypadku wersji nowszej wersji pomocniczej firma Microsoft zaprzestanie aktualizowania starszej wersji pomocniczej. Na przykład w przypadku wersji RHEL 7,6 RHEL 7,5 obrazy nie są już aktualizowane.

>[!NOTE]
> Aktywne maszyny wirtualne platformy Azure udostępniane z obrazów z opcją płatność zgodnie z rzeczywistym użyciem są połączone z usługą Azure RHUI i mogą otrzymywać aktualizacje i poprawki natychmiast po ich wydaniu przez Red Hat i zreplikowane na platformie Azure RHUI. Czas jest zwykle krótszy niż 24 godziny od oficjalnego wydania przez Red Hat. Te maszyny wirtualne nie wymagają nowego opublikowanego obrazu na potrzeby pobierania aktualizacji. Klienci mają pełną kontrolę nad tym, kiedy należy inicjować aktualizację.

## <a name="image-retention-policy"></a>Zasady przechowywania obrazów

Bieżące zasady mają na celu zachowanie wszystkich opublikowanych obrazów. Firma Microsoft zastrzega sobie prawo do usuwania obrazów, które są znane, aby powodowały problemy jakiegokolwiek rodzaju. Na przykład obrazy z nieprawidłowymi konfiguracjami ze względu na kolejne aktualizacje platformy lub składników mogą zostać usunięte. Obrazy, które mogą zostać usunięte, są zgodne z bieżącymi zasadami portalu Azure Marketplace w celu dostarczenia powiadomień do 30 dni przed usunięciem obrazu.

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę obrazów RHEL na platformie Azure, zobacz [obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure](./redhat-imagelist.md).
* Aby dowiedzieć się więcej na temat infrastruktury aktualizacji Red Hat platformy Azure, zobacz temat [infrastruktura aktualizacji Red Hat dla maszyn wirtualnych RHEL na żądanie na platformie Azure](./redhat-rhui.md).
* Aby dowiedzieć się więcej o ofercie RHEL BYOS, zobacz [Red Hat Enterprise Linux przenoszenie własnych subskrypcji na platformie Azure](./byos.md).
* Aby uzyskać informacje na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).
