---
title: 'Samouczek: Dodawanie węzłów do klastra plików usługi Azure FXT Edge'
description: Dowiedz się, jak dodać węzły klastra do pamięci podręcznej magazynu usługi Azure FXT Edge i włączyć funkcję wysokiej dostępności (HA).
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021881"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Samouczek: Dodawanie węzłów klastra do klastra plików usługi Azure FXT Edge

Nowy klaster usługi Azure FXT Edge jest tworzony z tylko jednym węzłem. Przed wykonaniem innej konfiguracji należy dodać co najmniej dwa węzły i włączyć wysoką dostępność.

W tym samouczku wyjaśniono, jak dodać węzły klastra i włączyć funkcję wysokiej dostępności (HA).

Z tego samouczka dowiesz się:

> [!div class="checklist"]
>
> * Jak dodać węzły do klastra FXT
> * Jak włączyć wysoką dostępność

Wykonanie kroków opisanych w tym samouczku zajmie około 45 minut.

Przed rozpoczęciem pracy z tym samouczkiem Włącz w węzłach, które chcesz dodać, i [Ustaw ich początkowe hasła](fxt-node-password.md).

## <a name="1-load-the-cluster-nodes-page"></a>1. Załaduj stronę węzłów klastra

Otwórz Panel sterowania klastra w przeglądarce internetowej i zaloguj się jako administrator. (Szczegółowe instrukcje znajdują się w artykule Omówienie, w obszarze [Otwórz strony ustawień](fxt-cluster-create.md#open-the-settings-pages)).

Panel sterowania pokazuje kartę **pulpit nawigacyjny** , gdy zostanie otwarta. 

![Pulpit nawigacyjny panelu sterowania (pierwsza karta)](media/fxt-cluster-config/dashboard-1-node.png)

Ten obraz przedstawia pulpit nawigacyjny nowo utworzonego klastra z pojedynczym węzłem.

## <a name="2-locate-the-node-to-add"></a>2. Zlokalizuj węzeł do dodania

Aby dodać węzły, kliknij kartę **Ustawienia** , a następnie wybierz stronę **węzły FXT** w sekcji **klaster** .

![Karta Ustawienia panelu sterowania (druga karta) z załadowanymi węzłami FXT > klastra](media/fxt-cluster-config/settings-fxt-nodes.png)

Lista **węzły FXT-unjoind** zawiera wszystkie nieprzypisane węzły FXT (większość centrów danych ma tylko kilka. Znajdź węzły FXT, które chcesz dodać do klastra.

> [!Tip]
> Jeśli nie możesz znaleźć węzła na liście **przyłączonych** , sprawdź, czy spełnia on następujące wymagania:
>
> * Jest on włączony i ma [ustawiony hasło główne](fxt-node-password.md).
> * Jest on połączony z siecią, do której można uzyskać dostęp. Jeśli używasz sieci VLAN, musi ona znajdować się w tej samej sieci VLAN co klaster.
> * Można go wykryć przy użyciu protokołu Bonjour.
>
>   Niektóre ustawienia zapory blokują porty TCP/UDP używane przez Bonjour, co uniemożliwia systemowi operacyjnemu FXT automatyczne wykrywanie węzłów.
>
> Jeśli węzeł, który chcesz dodać, nie znajduje się na liście, wypróbuj następujące rozwiązania:
>
> * Kliknij przycisk **odnajdywanie ręczne** , aby znaleźć go według adresu IP.
>
> * Ręcznie Przypisz tymczasowe adresy IP. Jest to rzadki przypadek, ale może być konieczne, jeśli używasz oznakowanych sieci VLAN, a węzły nie znajdują się w poprawnej sieci lub sieć nie zezwala na samodzielne adresy IP. Postępuj zgodnie z instrukcjami w starszej wersji tego dokumentu, aby [ręcznie ustawić statyczny adres IP](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nazwa węzła, adres IP, wersja oprogramowania i stan uprawnień są wyświetlane na liście. Zazwyczaj w kolumnie **stan** znajduje się komunikat "Przyłączanie" lub opisywanie problemu systemowego lub sprzętowego, który sprawia, że węzeł nie kwalifikuje się do dołączenia do klastra.

Kolumna **Akcje** zawiera przyciski umożliwiające dodanie węzła do klastra lub zaktualizowanie jego oprogramowania. Przycisk Aktualizuj automatycznie instaluje wersję oprogramowania zgodną z węzłami znajdującymi się już w klastrze.

Wszystkie węzły w klastrze muszą korzystać z tej samej wersji systemu operacyjnego, ale nie trzeba aktualizować oprogramowania przed dodaniem węzła. Po kliknięciu przycisku **Zezwól na przyłączenie** proces dołączania do klastra automatycznie sprawdza i instaluje oprogramowanie systemu operacyjnego zgodne z wersją w klastrze.

Aby dowiedzieć się więcej na temat opcji na tej stronie, Przeczytaj [   >  **węzeł FXT** klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) w podręczniku konfiguracji klastra.

## <a name="3-click-the-allow-to-join-button"></a>3. kliknij przycisk "Zezwól na przyłączenie"

Kliknij przycisk **Zezwól na przyłączenie** _ w kolumnie _ *Actions** dla węzła, który chcesz dodać.

Po kliknięciu przycisku stan węzła może ulec zmianie, ponieważ jego oprogramowanie zostało zaktualizowane w celu dodania go do klastra.

Poniższy obraz przedstawia węzeł, który jest w trakcie dołączania do klastra (najprawdopodobniej jest to przed dodaniem aktualizacji systemu operacyjnego). W kolumnie **Akcje** nie są wyświetlane żadne przyciski dla węzłów, które są dodawane do klastra.

![jeden wiersz tabeli węzła, pokazujący nazwę węzła, adres IP, wersję oprogramowania, komunikat "dozwolony do przyłączenia" i pustą ostatnią kolumnę](media/fxt-cluster-config/node-join-in-process.png)

Po kilku chwilach nowy węzeł powinien pojawić się na liście węzłów klastra w górnej części strony ustawień **węzłów FXT** .

Powtórz ten proces, aby dodać inne węzły do klastra. Nie musisz czekać, aż jeden węzeł zakończy dołączenie do klastra przed rozpoczęciem kolejnego.

## <a name="enable-high-availability"></a>Włącz wysoką dostępność

Po dodaniu drugiego węzła do klastra na pulpicie nawigacyjnym panelu sterowania może zostać wyświetlony komunikat ostrzegawczy informujący o tym, że funkcja wysokiej dostępności nie jest skonfigurowana.

Wysoka dostępność (HA) umożliwia węzłom klastra kompensację pod względem siebie, jeśli jeden z nich ulegnie awarii. HA nie jest domyślnie włączona.

![Karta pulpitu nawigacyjnego z komunikatem "klaster ma więcej niż jeden węzeł, ale HA nie jest włączona..." w tabeli warunków](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Nie włączaj HA do momentu, gdy w klastrze nie ma co najmniej trzech węzłów.

Wykonaj następującą procedurę, aby włączyć HA:

1. Załaduj stronę **wysoka dostępność** w sekcji **klaster** na karcie **Ustawienia** .

   ![Strona konfiguracji HA (> o wysokiej dostępności w klastrze). Pole wyboru "Włącz HA" znajduje się u góry, a przycisk Prześlij znajduje się u dołu.](media/fxt-cluster-config/enable-ha.png)

2. Kliknij pole z etykietą **Włącz ha** i kliknij przycisk **Prześlij** .

Na **pulpicie nawigacyjnym** zostanie wyświetlony alert z informacją o tym, że jest włączona opcja ha.

![Tabela pulpitów nawigacyjnych pokazująca komunikat "HA jest teraz w pełni skonfigurowany"](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Następne kroki

Po dodaniu wszystkich węzłów w klastrze Kontynuuj instalację, konfigurując magazyn długoterminowy klastra.

> [!div class="nextstepaction"]
> [Dodaj magazyn zaplecza i skonfiguruj wirtualną przestrzeń nazw](fxt-add-storage.md)