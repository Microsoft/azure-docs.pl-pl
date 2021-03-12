---
title: Replikacja geograficzna rejestru
description: Rozpocznij tworzenie rejestru kontenerów platformy Azure z replikacją geograficzną i zarządzanie nim, dzięki czemu rejestr ma udostępniać wiele regionów z wielogłównymi replikami regionalnymi. Replikacja geograficzna to funkcja warstwy Premium usługi.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 4e82be0e81e5e8c0182e061a0fba0f880bd45cc6
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632394"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Firmy, które chcą zaznaczyć swoją obecność lokalną lub wykonywać dynamiczną kopię zapasową, decydują się na uruchamianie usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem jest umieszczenie rejestru kontenerów w każdym regionie, w którym uruchamiane są obrazy. Umożliwia to wykonywanie operacji w pobliskiej sieci, dzięki czemu transfery w warstwie obrazu są szybkie i niezawodne. Replikacja geograficzna umożliwia działanie rejestru kontenerów platformy Azure jako pojedynczego rejestru obsługującego wiele regionów przy użyciu wielowzorcowych rejestrów regionalnych. 

Rejestr z replikacją geograficzną zapewnia następujące korzyści:

* Nazwy pojedynczego rejestru, obrazu i tagu można używać w wielu regionach
* Poprawianie wydajności i niezawodności wdrożeń regionalnych przy użyciu sieci i dostępu do rejestru
* Zmniejsz koszty transferu danych, pobierając warstwy obrazu z lokalnego, zreplikowanego rejestru w tym samym lub w pobliżu regionu, w którym znajduje się host kontenera
* Ujednolicone zarządzanie rejestrem w wielu regionach
* Odporność na rejestry w przypadku wystąpienia awarii regionalnej

> [!NOTE]
> Jeśli zachodzi potrzeba obsługi kopii obrazów kontenerów w więcej niż jednym rejestrze kontenerów platformy Azure, usługa Azure Container Registry obsługuje również [importowanie obrazów](container-registry-import-images.md). Na przykład w ramach przepływu pracy DevOps można zaimportować obraz z rejestru deweloperskiego do rejestru produkcyjnego bez konieczności używania poleceń platformy Docker.
>

## <a name="example-use-case"></a>Przykładowy przypadek użycia
Firma Contoso posiada dostępną publicznie witryny internetową zlokalizowaną w Stanach Zjednoczonych, Kanadzie i Europie. Aby obsługiwać te rynki przy użyciu zawartości lokalnej dostępnej w pobliskiej sieci, firma Contoso uruchamia klastry usługi [Azure Kubernetes Service](../aks/index.yml) (AKS) w regionach Zachodnie stany USA, Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Aplikacja internetowa wdrożona jako obraz platformy Docker korzysta z tego samego kodu i obrazu we wszystkich regionach. Zawartość, lokalna w danym regionie, jest pobierana z bazy danych aprowizowanej w sposób unikatowy w każdym regionie. Dla każdego wdrożenia regionalnego istnieje unikatowa konfiguracja zasobów, takich jak lokalna baza danych.

Zespół programistyczny znajduje się w Seattle w stanie Waszyngton i korzysta z centrum danych w regionie Zachodnie stany USA.

![Wypychanie do wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Wypychanie do wielu rejestrów*

Zanim były używane funkcje replikacji geograficznej, firma Contoso posiadała rejestr znajdujący się w regionie Zachodnie stany USA oraz dodatkowy rejestr w regionie Europa Zachodnia. W celu obsługi tych regionów zespół programistyczny wypychał obrazy do dwóch różnych rejestrów.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Ściąganie z wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ściąganie z wielu rejestrów*

Typowe wyzwania związane z korzystaniem z wielu rejestrów:

* Klastry w regionach Wschodnie stany USA, Zachodnie stany USA i Kanada Środkowa są ściągane z rejestru w regionie Zachodnie stany USA, co powoduje naliczanie opłat za ruch wychodzący, ponieważ każdy z tych zdalnych hostów kontenerów ściąga obrazy z centrów danych w regionie Zachodnie stany USA.
* Zespół programistyczny musi wypychać obrazy do rejestrów w regionach Zachodnie stany USA i Europa Zachodnia.
* Zespół programistyczny musi konfigurować i konserwować każde wdrożenie regionalne przy użyciu nazw obrazów odwołujących się do rejestru lokalnego.
* Dostęp do rejestru musi być skonfigurowany dla każdego regionu.

## <a name="benefits-of-geo-replication"></a>Korzyści z replikacji geograficznej

![Ściąganie z rejestru z replikacją geograficzną](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Funkcja replikacji geograficznej usługi Azure Container Registry zapewnia następujące korzyści:

* Zarządzanie pojedynczym rejestrem w wielu regionach: `contoso.azurecr.io`
* Zarządzaj pojedynczą konfiguracją wdrożeń obrazów, ponieważ wszystkie regiony używają tego samego obrazu URL: `contoso.azurecr.io/public/products/web:1.2`
* Wypchnij do jednego rejestru, podczas gdy ACR zarządza replikacją geograficzną. ACR replikuje tylko unikatowe warstwy, zmniejszając transfer danych między regionami. 
* Skonfiguruj regionalne elementy [webhook](container-registry-webhook.md) w celu powiadomienia o zdarzeniach w określonych replikach.
* Dostarcz rejestr o wysokiej dostępności odporny na awarie regionalne.

Azure Container Registry obsługuje również [strefy dostępności](zone-redundancy.md) w celu utworzenia odpornego i wysokiej dostępności rejestru kontenerów platformy Azure w regionie świadczenia usługi Azure. Połączenie stref dostępności w celu zapewnienia nadmiarowości w obrębie regionu i replikacji geograficznej w wielu regionach zwiększa niezawodność i wydajność rejestru.

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej

Konfigurowanie replikacji geograficznej jest równie proste, co klikanie regionów na mapie. Replikacją geograficzną można także zarządzać za pomocą narzędzi, w tym poleceń [AZ ACR Replication](/cli/azure/acr/replication) w interfejsie wiersza polecenia platformy Azure, lub wdrożyć rejestr obsługujący replikację geograficzną przy użyciu [szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Replikacja geograficzna to funkcja [rejestrów Premium](container-registry-skus.md). Jeśli Twój rejestr nie znajduje się jeszcze w warstwie Premium, możesz zmienić warstwę z warstwy Podstawowa i Standardowa na warstwę Premium w [witrynie Azure Portal](https://portal.azure.com):

![Przełączanie warstw usług w Azure Portal](media/container-registry-skus/update-registry-sku.png)

Aby skonfigurować replikację geograficzną dla rejestru w warstwie Premium, zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Przejdź do używanego rejestru Azure Container Registry, a następnie wybierz pozycję **Replikacje**:

![Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zostanie wyświetlona mapa przedstawiająca wszystkie bieżące regiony platformy Azure:

 ![Mapa regionów w witrynie Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Niebieskie sześciokąty reprezentują bieżące repliki
* Zielone sześciokąty reprezentują możliwe regiony replik
* Szare sześciokąty reprezentują regiony platformy Azure, które nie są jeszcze dostępne dla replikacji

Aby skonfigurować replikę, wybierz zielony sześciokąt, a następnie wybierz pozycję **Utwórz**:

 ![Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal](media/container-registry-geo-replication/create-replication.png)

Aby skonfigurować dodatkowe repliki, wybierz zielone sześciokąty dla innych regionów, a następnie kliknij pozycję **Utwórz**.

Usługa ACR rozpocznie synchronizowanie obrazów między skonfigurowanymi replikami. Po ukończeniu tego zadania w portalu zostanie wyświetlony stan *Gotowe*. Stan repliki w portalu nie jest automatycznie aktualizowany. Użyj przycisku odświeżania, aby wyświetlić zaktualizowany stan.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Zagadnienia dotyczące korzystania z rejestru z replikacją geograficzną

* Każdy region w rejestrze z replikacją geograficzną jest niezależny od konfiguracji. Azure Container Registry umowy SLA mają zastosowanie do każdego regionu zreplikowanego geograficznie.
* W przypadku wypychania lub ściągania obrazów z rejestru z replikacją geograficzną usługa Azure Traffic Manager w tle wysyła żądanie do rejestru znajdującego się najbliżej Ciebie w odniesieniu do opóźnienia sieci.
* Po wypchnięciu aktualizacji obrazu lub tagu do najbliższego regionu przez Azure Container Registry replikację manifestów i warstw do pozostałych regionów, które zostały wybrane. Większe obrazy trwają dłużej niż mniejsze. Obrazy i Tagi są synchronizowane w regionach replikacji z modelem spójności ostatecznej.
* Aby zarządzać przepływami pracy, które są zależne od aktualizacji wypychanych do rejestru z replikacją geograficzną, zalecamy skonfigurowanie elementów [webhook](container-registry-webhook.md) w celu reagowania na zdarzenia wypychania. Można skonfigurować regionalne elementy webhook w ramach rejestru replikowanego geograficznie do śledzenia zdarzeń wypychania w miarę ich kończenia w regionach replikowanych geograficznie.
* Aby obsłużać obiekty blob reprezentujące warstwy zawartości, Azure Container Registry używa punktów końcowych danych. Możesz włączyć [dedykowane punkty końcowe danych](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) dla rejestru w każdym z geograficznie replikowanych regionów rejestru. Punkty końcowe umożliwiają konfigurację ścisłych reguł dostępu do zapory z zakresem. W celu rozwiązywania problemów można opcjonalnie [wyłączyć routing do replikacji](#temporarily-disable-routing-to-replication) przy zachowaniu replikowanych danych.
* W przypadku skonfigurowania [prywatnego linku](container-registry-private-link.md) do rejestru przy użyciu prywatnych punktów końcowych w sieci wirtualnej, dedykowane punkty końcowe danych w każdym z replikowanych geograficznie regionów są domyślnie włączone. 

## <a name="delete-a-replica"></a>Usuwanie repliki

Po skonfigurowaniu repliki dla rejestru możesz ją usunąć w dowolnym momencie, jeśli nie jest już potrzebne. Usuń replikę za pomocą Azure Portal lub innych narzędzi, takich jak polecenie [AZ ACR Replication Delete](/cli/azure/acr/replication#az-acr-replication-delete) w interfejsie wiersza polecenia platformy Azure.

Aby usunąć replikę z Azure Portal:

1. Przejdź do Azure Container Registry i wybierz pozycję **replikacje**.
1. Wybierz nazwę repliki, a następnie wybierz pozycję **Usuń**. Potwierdź, że chcesz usunąć replikę.

Aby użyć interfejsu wiersza polecenia platformy Azure do usunięcia repliki *rejestru* w regionie Wschodnie stany USA:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Cennik replikacji geograficznej

Replikacja geograficzna to funkcja [warstwy usługi Premium](container-registry-skus.md) Azure Container Registry. Replikacja rejestru w żądanych regionach wiąże się z naliczaniem opłat za rejestr w warstwie Premium w danym regionie.

W poprzednim przykładzie firma Contoso skonsolidowała dwa rejestry w ramach jednego, dodając repliki do regionu Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Firma Contoso zapłaciłaby miesięcznie czterokrotną opłatę za warstwę Premium bez dodatkowej konfiguracji ani zarządzania. W każdym regionie obrazy są teraz ściągane lokalnie, co zwiększa wydajność i niezawodność bez ponoszenia opłat za ruch wychodzący w sieci z regionu Zachodnie stany USA do regionu Kanada Środkowa i Wschodnie stany USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Rozwiązywanie problemów z operacjami wypychania z rejestrami replikowanymi geograficznie
 
Klient platformy Docker, który wypycha obraz do rejestru z replikacją geograficzną, może nie wypchnąć wszystkich warstw obrazu i jego manifestu do jednego zreplikowanego regionu. Może to być spowodowane tym, że usługa Azure Traffic Manager kieruje żądania rejestru do najbliższego rejestru replikowanego w sieci. Jeśli rejestr ma dwa regiony replikacji w *pobliżu* , warstwy obrazu i manifest mogą być dystrybuowane do dwóch lokacji, a operacja push kończy się niepowodzeniem po sprawdzeniu poprawności manifestu. Ten problem występuje ze względu na sposób, w jaki nazwa DNS rejestru jest rozpoznawana na niektórych hostach z systemem Linux. Ten problem nie występuje w systemie Windows, co zapewnia pamięć podręczną usługi DNS po stronie klienta.
 
W przypadku wystąpienia tego problemu jedno rozwiązanie ma zastosowanie pamięci podręcznej DNS po stronie klienta, takiej jak `dnsmasq` na hoście z systemem Linux. Pozwala to zapewnić spójność nazwy rejestru. Jeśli używasz maszyny wirtualnej z systemem Linux na platformie Azure do wypychania do rejestru, zobacz Opcje [rozpoznawania nazw DNS dla maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/linux/azure-dns.md).

W celu zoptymalizowania rozpoznawania nazw DNS do najbliższej repliki podczas wypychania obrazów Skonfiguruj rejestr z replikacją geograficzną w tych samych regionach platformy Azure jako źródło operacji wypychania lub najbliższy Region podczas pracy poza platformą Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Tymczasowe wyłączenie routingu do replikacji

Aby rozwiązać problemy z rejestrem z replikacją geograficzną, można tymczasowo wyłączyć routing Traffic Manager do co najmniej jednej replikacji. Począwszy od interfejsu wiersza polecenia platformy Azure w wersji 2,8, można skonfigurować `--region-endpoint-enabled` opcję (wersja zapoznawcza) podczas tworzenia lub aktualizowania zreplikowanego regionu. Po ustawieniu `--region-endpoint-enabled` opcji replikacji na `false` , Traffic Manager nie ma już trasy wypychania ani żądań ściągnięcia platformy Docker do tego regionu. Domyślnie Routing do wszystkich replikacji jest włączony, a synchronizacja danych między wszystkimi replikacjami odbywa się niezależnie od tego, czy Routing jest włączony, czy wyłączony.

Aby wyłączyć routing do istniejącej replikacji, najpierw uruchom polecenie [AZ ACR Replication list][az-acr-replication-list] , aby wyświetlić listę replikacji w rejestrze. Następnie uruchom [AZ ACR Replication Update][az-acr-replication-update] i Set `--region-endpoint-enabled false` dla określonej replikacji. Na przykład, aby skonfigurować ustawienie dla replikacji *zachodniej* w *rejestrze*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Aby przywrócić Routing do replikacji:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z trzyczęściową serią samouczków dostępną w sekcji [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md). Opisano w niej tworzenie rejestru z replikacją geograficzną, tworzenie kontenera, a następnie wdrażanie go w ramach wielu regionalnych aplikacji usługi Web Apps na potrzeby wystąpień kontenerów za pomocą pojedynczego polecenia `docker push`.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
