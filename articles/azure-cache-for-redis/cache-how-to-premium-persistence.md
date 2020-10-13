---
title: Konfigurowanie trwałości danych — pamięć podręczna systemu Azure w warstwie Premium dla Redis
description: Dowiedz się, jak skonfigurować trwałość danych i zarządzać nimi w usłudze Azure cache dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: fbfd384787d35317a4e45c4f91cf8a3ad4ba5a61
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000009"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować trwałość danych dla pamięci podręcznej systemu Azure w warstwie Premium dla Redis
W tym artykule dowiesz się, jak skonfigurować trwałość w pamięci podręcznej systemu Azure w warstwie Premium dla wystąpienia Redis za pomocą Azure Portal. Usługa Azure cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru i funkcji pamięci podręcznej, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. 

## <a name="what-is-data-persistence"></a>Co to jest trwałość danych?
[Trwałość Redis](https://redis.io/topics/persistence) umożliwia utrwalanie danych przechowywanych w Redis. Można również tworzyć migawki i tworzyć kopie zapasowe danych, które można ładować w przypadku awarii sprzętu. Jest to ogromna korzyść w porównaniu z warstwą podstawową lub standardową, w której wszystkie dane są przechowywane w pamięci i może istnieć potencjalna utrata danych w przypadku awarii, w której węzły pamięci podręcznej nie działają. 

Usługa Azure cache for Redis oferuje Trwałość Redis przy użyciu następujących modeli:

* Trwałość **RDB** — w przypadku skonfigurowania trwałości pliku RDB (Redis Database) usługa Azure cache for Redis utrzymuje migawkę pamięci podręcznej platformy Azure dla Redis w formacie binarnym Redis na dysk na podstawie konfigurowalnej częstotliwości kopii zapasowych. W przypadku wystąpienia krytycznego zdarzenia, które powoduje wyłączenie zarówno podstawowej, jak i pamięci podręcznej repliki, pamięć podręczną jest tworzona przy użyciu najnowszej migawki. Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#rdb-advantages) i [wadach](https://redis.io/topics/persistence#rdb-disadvantages) trwałości RDB.
* **Kopia zapasowa AOF trwałość** — w przypadku skonfigurowania trwałości kopia zapasowa AOF (dołączania tylko pliku) pamięć podręczna systemu Azure dla Redis zapisuje każdą operację zapisu do dziennika, który jest zapisywany co najmniej raz na sekundę na koncie usługi Azure Storage. W przypadku wystąpienia krytycznego zdarzenia, które powoduje wyłączenie zarówno podstawowej, jak i pamięci podręcznej repliki, pamięć podręczną jest tworzona przy użyciu przechowywanych operacji zapisu. Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#aof-advantages) i [wadach](https://redis.io/topics/persistence#aof-disadvantages) trwałości kopia zapasowa AOF.

Trwałość zapisuje dane Redis do konta usługi Azure Storage, którego jesteś własnością i którymi zarządzasz. Można skonfigurować z nowego bloku **Azure cache for Redis** podczas tworzenia pamięci podręcznej i w **menu zasób** dla istniejących pamięci podręcznych Premium.

> [!NOTE]
> 
> Usługa Azure Storage automatycznie szyfruje dane, gdy są utrwalane. Do szyfrowania można użyć własnych kluczy. Aby uzyskać więcej informacji, zobacz [klucze zarządzane przez klienta z Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

1. Aby utworzyć pamięć podręczną Premium, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. Oprócz tworzenia pamięci podręcznych w witrynie Azure Portal, możesz również utworzyć je przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure oraz szablonów usługi Resource Manager. Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej platformy Azure dla usługi Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Utwórz zasób.":::
   
2. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Utwórz zasób.":::

3. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia nowej pamięci podręcznej Premium.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, które zawierają tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | I wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | I wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Typ pamięci podręcznej** | Wybierz pozycję Pamięć podręczna Premium, aby skonfigurować funkcje Premium. Aby uzyskać szczegółowe informacje, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |

4. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

5. Na karcie **Sieć** wybierz metodę łączności. W przypadku wystąpień pamięci podręcznej Premium można łączyć się publicznie za pośrednictwem publicznych adresów IP lub punktów końcowych usług albo prywatnie przy użyciu prywatnego punktu końcowego.

6. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

7. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowania i trwałości danych. W celu zapewnienia trwałości danych możesz wybrać opcję trwałość **RDB** lub **kopia zapasowa AOF** . 

8. Aby włączyć trwałość RDB, kliknij pozycję **RDB** i skonfiguruj ustawienia. 
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Częstotliwość tworzenia kopii zapasowych** | Lista rozwijana i wybór interwału kopii zapasowych, dostępne opcje to **15 minut**, **30 minut**, **60 minut**, **6 godzin**, **12**godzin i **24 godziny**. | Ten interwał rozpoczyna zliczanie w dół po pomyślnym zakończeniu poprzedniej operacji tworzenia kopii zapasowej i po zainicjowaniu nowej kopii zapasowej. | 
   | **Konto magazynu** | I wybierz konto magazynu. | Musisz wybrać konto magazynu w tym samym regionie, w którym znajduje się pamięć podręczna, a konto **Premium Storage** jest zalecane, ponieważ usługa Premium Storage ma wyższą przepływność.  | 
   | **Klucz magazynu** | Wybierz pozycję **klucz podstawowy** lub **klucz pomocniczy** do użycia. | W przypadku ponownego wygenerowania klucza magazynu dla konta trwałości należy ponownie skonfigurować żądany klucz z listy rozwijanej **klucza magazynu** . | 

    Pierwsza kopia zapasowa jest inicjowana po upłynięciu interwału częstotliwości tworzenia kopii zapasowych.

9. Aby włączyć trwałość kopia zapasowa AOF, kliknij pozycję **kopia zapasowa AOF** i skonfiguruj ustawienia. 
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Pierwsze konto magazynu** | I wybierz konto magazynu. | To konto magazynu musi znajdować się w tym samym regionie, w którym znajduje się pamięć podręczna, a konto **Premium Storage** jest zalecane, ponieważ usługa Premium Storage ma wyższą przepływność. | 
   | **Pierwszy klucz magazynu** | Wybierz pozycję **klucz podstawowy** lub **klucz pomocniczy** do użycia. | W przypadku ponownego wygenerowania klucza magazynu dla konta trwałości należy ponownie skonfigurować żądany klucz z listy rozwijanej **klucza magazynu** . | 
   | **Drugie konto magazynu** | Obowiązkowe Wybierz pozycję **klucz podstawowy** lub **klucz pomocniczy** do użycia. | Opcjonalnie możesz skonfigurować dodatkowe konto magazynu. W przypadku skonfigurowania drugiego konta magazynu operacje zapisu w pamięci podręcznej repliki są zapisywane na tym drugim koncie magazynu. | 
   | **Drugi klucz magazynu** | Obowiązkowe Wybierz pozycję **klucz podstawowy** lub **klucz pomocniczy** do użycia. | W przypadku ponownego wygenerowania klucza magazynu dla konta trwałości należy ponownie skonfigurować żądany klucz z listy rozwijanej **klucza magazynu** . | 

    Po włączeniu funkcji trwałości kopia zapasowa AOF operacje zapisu w pamięci podręcznej są zapisywane na wyznaczynym koncie magazynu (lub na kontach, jeśli skonfigurowano drugie konto magazynu). W przypadku błędu krytycznego, który przeprowadzi zarówno podstawową, jak i pamięć podręczną repliki, zapisany dziennik kopia zapasowa AOF służy do odbudowywania pamięci podręcznej.

10. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

11. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

12. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

13. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd**usługi Azure cache for Redis   . Gdy **stan**   jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 

## <a name="persistence-faq"></a>Często zadawane pytania dotyczące trwałości
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure cache for Redis trwałość.

* [Czy mogę włączyć trwałość w wcześniej utworzonej pamięci podręcznej?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Czy mogę włączyć trwałość kopia zapasowa AOF i RDB w tym samym czasie?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Który model trwałości należy wybrać?](#which-persistence-model-should-i-choose)
* [Co się stanie, jeśli przeprowadzono skalowanie do innego rozmiaru i przywrócono kopię zapasową wykonaną przed operacją skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trwałość RDB
* [Czy mogę zmienić częstotliwość tworzenia kopii zapasowych RDB po utworzeniu pamięci podręcznej?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Dlaczego jeśli mam częstotliwość tworzenia kopii zapasowych RDB 60 minut między kopiami zapasowymi jest więcej niż 60 minut?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co się stanie z poprzednimi kopiami zapasowymi RDB po utworzeniu nowej kopii zapasowej?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Trwałość kopia zapasowa AOF
* [Kiedy należy używać drugiego konta magazynu?](#when-should-i-use-a-second-storage-account)
* [Czy trwałość kopia zapasowa AOF ma wpływ na czas oczekiwania lub wydajność mojej pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak usunąć drugie konto magazynu?](#how-can-i-remove-the-second-storage-account)
* [Co to jest ponowne zapisywanie i jak ma to wpływ na moją pamięć podręczną?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co należy oczekiwać podczas skalowania pamięci podręcznej z włączonym kopia zapasowa AOF?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Jak moje dane kopia zapasowa AOF są zorganizowane w magazynie?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Czy mogę włączyć trwałość w wcześniej utworzonej pamięci podręcznej?
Tak, trwałość Redis można skonfigurować zarówno podczas tworzenia pamięci podręcznej, jak i w istniejących pamięciach podręcznych Premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Czy mogę włączyć trwałość kopia zapasowa AOF i RDB w tym samym czasie?

Nie, można włączyć tylko obiekty RDB lub kopia zapasowa AOF, ale nie oba jednocześnie.

### <a name="which-persistence-model-should-i-choose"></a>Który model trwałości należy wybrać?

KOPIA zapasowa AOF trwałość zapisuje każdy zapis w dzienniku, który ma wpływ na przepływność, w porównaniu z trwałością RDB, która zapisuje kopie zapasowe na podstawie skonfigurowanego interwału tworzenia kopii zapasowych, przy minimalnym wpływie na wydajność. Wybierz pozycję Kopia zapasowa AOF trwałość, jeśli głównym celem jest Minimalizacja utraty danych, i możesz obsłużyć spadek przepływności dla pamięci podręcznej. Wybierz pozycję trwałość RDB, jeśli chcesz zachować optymalną przepływność pamięci podręcznej, ale nadal chcesz przeprowadzić odzyskiwanie danych.

* Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#rdb-advantages) i [wadach](https://redis.io/topics/persistence#rdb-disadvantages) trwałości RDB.
* Dowiedz się więcej o [zaletach](https://redis.io/topics/persistence#aof-advantages) i [wadach](https://redis.io/topics/persistence#aof-disadvantages) trwałości kopia zapasowa AOF.

Aby uzyskać więcej informacji o wydajności przy korzystaniu z trwałości kopia zapasowa AOF, zobacz [czy trwałość kopia zapasowa AOF ma wpływ na cały czas oczekiwania lub wydajność mojej pamięci podręcznej?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co się stanie, jeśli przeprowadzono skalowanie do innego rozmiaru i przywrócono kopię zapasową wykonaną przed operacją skalowania?

W przypadku trwałości programu RDB i kopia zapasowa AOF:

* W przypadku skalowania do większego rozmiaru nie ma to żadnego wpływu.
* W przypadku skalowania do mniejszego rozmiaru i ustawienia niestandardowych [baz danych](cache-configure.md#databases) , które są większe niż [Limit baz danych](cache-configure.md#databases) dla nowego rozmiaru, dane w tych bazach danych nie są przywracane. Aby uzyskać więcej informacji, zobacz [Ustawienia moje niestandardowe bazy danych, których to dotyczy, podczas skalowania?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* W przypadku skalowania do mniejszego rozmiaru i braku miejsca na mniejszym rozmiarze do przechowywania wszystkich danych z ostatniej kopii zapasowej klucze zostaną wykluczone podczas procesu przywracania, zazwyczaj przy użyciu zasad wykluczania [AllKeys-LRU](https://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Czy mogę zmienić częstotliwość tworzenia kopii zapasowych RDB po utworzeniu pamięci podręcznej?
Tak, możesz zmienić częstotliwość tworzenia kopii zapasowych dla trwałości RDB w bloku **trwałość danych** . Aby uzyskać instrukcje, zobacz Konfigurowanie trwałości Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Dlaczego jeśli mam częstotliwość tworzenia kopii zapasowych RDB 60 minut między kopiami zapasowymi jest więcej niż 60 minut?
Interwał częstotliwości kopii zapasowych w przypadku trwałości RDB nie zostanie uruchomiony do momentu pomyślnego zakończenia poprzedniego procesu tworzenia kopii zapasowej. Jeśli częstotliwość tworzenia kopii zapasowych wynosi 60 minut, a proces tworzenia kopii zapasowej trwa 15 minut, kolejna kopia zapasowa nie zostanie uruchomiona do 75 minut od czasu rozpoczęcia poprzedniej kopii zapasowej.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co się stanie z poprzednimi kopiami zapasowymi RDB po utworzeniu nowej kopii zapasowej?
Wszystkie kopie zapasowe o trwałości RDB z wyjątkiem najnowszej z nich zostaną automatycznie usunięte. To usunięcie może nie nastąpić natychmiast, ale starsze kopie zapasowe nie są utrwalane w nieskończoność.


### <a name="when-should-i-use-a-second-storage-account"></a>Kiedy należy używać drugiego konta magazynu?

Jeśli uważasz, że w pamięci podręcznej jest większa niż oczekiwana operacja ustawiania, należy użyć drugiego konta magazynu do kopia zapasowa AOF trwałości.  Skonfigurowanie konta magazynu pomocniczego pomaga zapewnić, że pamięć podręczna nie osiągnie limitów przepustowości magazynu.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Czy trwałość kopia zapasowa AOF ma wpływ na czas oczekiwania lub wydajność mojej pamięci podręcznej?

Trwałość kopia zapasowa AOF ma wpływ na przepływność przez około 15% – 20%, gdy pamięć podręczna jest mniejsza niż maksymalne obciążenie (obciążenie procesora CPU i serwera w obszarze 90%). Występują problemy z opóźnieniami, gdy pamięć podręczna mieści się w ramach tych limitów. Jednak pamięć podręczna osiągnie te limity wcześniej z włączonym kopia zapasowa AOF.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak usunąć drugie konto magazynu?

Konto magazynu pomocniczego trwałości kopia zapasowa AOF można usunąć, ustawiając drugie konto magazynu jako takie samo, jak pierwsze konto magazynu. W przypadku istniejących pamięci podręcznych blok **trwałość danych** jest dostępny z **menu zasób** dla pamięci podręcznej. Aby wyłączyć trwałość kopia zapasowa AOF, kliknij pozycję **wyłączone**.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co to jest ponowne zapisywanie i jak ma to wpływ na moją pamięć podręczną?

Gdy plik kopia zapasowa AOF jest wystarczająco duży, ponowne zapisywanie jest automatycznie umieszczane w kolejce w pamięci podręcznej. Ponowne zapis zmienia rozmiar pliku kopia zapasowa AOF z minimalnym zestawem operacji wymaganych do utworzenia bieżącego zestawu danych. Podczas ponownego zapisywania należy oczekiwać, że osiągnięto limity wydajności wkrótce, szczególnie podczas pracy z dużymi zestawami danych. Ponowne Zapisywanie odbywa się rzadziej, gdy plik kopia zapasowa AOF stanie się większy, ale zajmie dużo czasu, gdy wystąpi.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co należy oczekiwać podczas skalowania pamięci podręcznej z włączonym kopia zapasowa AOF?

Jeśli plik kopia zapasowa AOF w czasie skalowania jest bardzo duży, oczekiwano, że operacja skalowania trwa dłużej niż oczekiwano, ponieważ będzie ponownie ładować plik po zakończeniu skalowania.

Aby uzyskać więcej informacji na temat skalowania, zobacz [co się stanie, jeśli przeskaluję do innego rozmiaru i przywrócono kopię zapasową wykonaną przed operacją skalowania?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Jak moje dane kopia zapasowa AOF są zorganizowane w magazynie?

Dane przechowywane w plikach kopia zapasowa AOF są podzielone na wiele stronicowych obiektów BLOB na węzeł w celu zwiększenia wydajności zapisywania danych w magazynie. W poniższej tabeli przedstawiono liczbę stronicowych obiektów BLOB używanych dla każdej warstwy cenowej:

| Warstwa Premium | Obiekty blob |
|--------------|-------|
| P1           | 4 za fragmentu    |
| P2           | 8 za fragmentu    |
| P3           | 16 na fragmentu   |
| P4           | 20 za fragmentu   |

Gdy klastrowanie jest włączone, każda fragmentu w pamięci podręcznej ma swój własny zestaw stronicowych obiektów blob, jak wskazano w poprzedniej tabeli. Na przykład pamięć podręczna P2 z trzema fragmentów dystrybuuje swój plik kopia zapasowa AOF w 24-stronicowych obiektach Blob (8 obiektów BLOB na fragmentu, z 3 fragmentów).

Po ponownej operacji zapisu w magazynie istnieją dwa zestawy plików kopia zapasowa AOF. Ponowne Zapisywanie odbywa się w tle i dołącza do pierwszego zestawu plików, podczas gdy ustawienia operacji, które są wysyłane do pamięci podręcznej podczas ponownego zapisu, dołączane do drugiego zestawu. Kopia zapasowa jest tymczasowo przechowywana podczas ponownego zapisywania w przypadku awarii, ale jest natychmiast usuwana po zakończeniu ponownego zapisywania.


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
