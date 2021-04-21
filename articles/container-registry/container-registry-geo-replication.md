---
title: Replikacja geograficzna rejestru
description: Wprowadzenie do tworzenia rejestru kontenerów platformy Azure z replikacją geograficzną i zarządzania nim, co umożliwia rejestrowi korzystanie z wielu regionów z wielowęzłańcową repliką regionalną. Replikacja geograficzna jest funkcją warstwy usługi Premium.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 3e5b064ec37b855186f633677e2b1a3f615a6736
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783867"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Firmy, które chcą zaznaczyć swoją obecność lokalną lub wykonywać dynamiczną kopię zapasową, decydują się na uruchamianie usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem jest umieszczenie rejestru kontenerów w każdym regionie, w którym uruchamiane są obrazy. Umożliwia to wykonywanie operacji w pobliskiej sieci, dzięki czemu transfery w warstwie obrazu są szybkie i niezawodne. Replikacja geograficzna umożliwia działanie rejestru kontenerów platformy Azure jako pojedynczego rejestru obsługującego wiele regionów przy użyciu wielowzorcowych rejestrów regionalnych. 

Rejestr z replikacją geograficzną zapewnia następujące korzyści:

* Nazwy pojedynczych rejestrów, obrazów i tagów mogą być używane w wielu regionach
* Poprawianie wydajności i niezawodności wdrożeń regionalnych przy użyciu dostępu do rejestru blisko sieci
* Zmniejsz koszty transferu danych, ściągając warstwy obrazu z lokalnego, replikowanego rejestru w tym samym lub pobliskim regionie co host kontenera
* Ujednolicone zarządzanie rejestrem w wielu regionach
* Odporność rejestru w przypadku wystąpienia awarii regionalnej

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
* Zarządzaj pojedynczą konfiguracją wdrożeń obrazów, ponieważ wszystkie regiony używają tego samego adresu URL obrazu: `contoso.azurecr.io/public/products/web:1.2`
* Wypychanie do pojedynczego rejestru, podczas gdy usługi ACR zarządza replikacją geograficzną. ACR replikuje tylko unikatowe warstwy, co zmniejsza transfer danych między regionami. 
* Skonfiguruj regionalne [webhook,](container-registry-webhook.md) aby powiadamiać o zdarzeniach w określonych replikach.
* Udostępnij rejestr o wysokiej dostępie, który jest odporny na regionalne błędy.

Azure Container Registry obsługuje również [strefy dostępności w](zone-redundancy.md) celu utworzenia odpornego i wysokiej dostępności rejestru kontenerów platformy Azure w regionie świadczenia usługi Azure. Kombinacja stref dostępności w celu zapewnienia nadmiarowości w obrębie regionu i replikacji geograficznej w wielu regionach zwiększa niezawodność i wydajność rejestru.

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej

Konfigurowanie replikacji geograficznej jest równie proste, co klikanie regionów na mapie. Możesz również zarządzać replikacją geograficzną przy użyciu narzędzi, takich jak [polecenia az acr replication](/cli/azure/acr/replication) w interfejsie wiersza polecenia platformy Azure, lub wdrożyć rejestr z włączoną replikacją geograficzną za pomocą szablonu Azure Resource Manager [usługi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Replikacja geograficzna jest funkcją [rejestrów Premium.](container-registry-skus.md) Jeśli Twój rejestr nie znajduje się jeszcze w warstwie Premium, możesz zmienić warstwę z warstwy Podstawowa i Standardowa na warstwę Premium w [witrynie Azure Portal](https://portal.azure.com):

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

* Po skonfigurowaniu każdy region w rejestrze z replikacją geograficzną jest niezależny. Azure Container Registry SLA mają zastosowanie do każdego regionu replikowanego geograficznie.
* Gdy wypychasz lub ściągasz obrazy z rejestru z replikacją geograficzną, program Azure Traffic Manager w tle wysyła żądanie do rejestru znajdującego się w regionie, który znajduje się najbliżej Ciebie, pod względem opóźnienia sieci.
* Po wypchnięciu aktualizacji obrazu lub tagu do najbliższego regionu replikacja manifestów i warstw Azure Container Registry do pozostałych regionów, w których została wybrana. Replikacja większych obrazów trwa dłużej niż w przypadku mniejszych. Obrazy i tagi są synchronizowane między regionami replikacji przy użyciu modelu spójności ostateczna.
* Aby zarządzać przepływami pracy, które są zależne od aktualizacji wypychanych do rejestru z replikacją geograficzną, zalecamy skonfigurowanie [webhook](container-registry-webhook.md) w celu reagowania na zdarzenia wypychania. Regionalne webhook można skonfigurować w rejestrze z replikacją geograficzną, aby śledzić zdarzenia wypychania w czasie ich ukończenia w regionach replikowanych geograficznie.
* Aby obsługiwać obiekty blob reprezentujące warstwy zawartości, Azure Container Registry punkty końcowe danych. Dedykowane punkty [końcowe danych dla](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) rejestru można włączyć w każdym z regionów rejestru z replikacją geograficzną. Te punkty końcowe umożliwiają konfigurację ściśle ograniczonych reguł dostępu zapory. Na potrzeby rozwiązywania problemów można opcjonalnie wyłączyć [routing do](#temporarily-disable-routing-to-replication) replikacji przy zachowaniu replikowanych danych.
* W przypadku [](container-registry-private-link.md) skonfigurowania linku prywatnego dla rejestru przy użyciu prywatnych punktów końcowych w sieci wirtualnej dedykowane punkty końcowe danych w każdym z regionów replikowanych geograficznie są domyślnie włączone. 

## <a name="delete-a-replica"></a>Usuwanie repliki

Po skonfigurowaniu repliki dla rejestru można ją usunąć w dowolnym momencie, jeśli nie jest już potrzebna. Usuń replikę przy użyciu Azure Portal lub innych narzędzi, takich jak [polecenie az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) w interfejsie wiersza polecenia platformy Azure.

Aby usunąć replikę w Azure Portal:

1. Przejdź do swojego Azure Container Registry i wybierz **pozycję Replikacje.**
1. Wybierz nazwę repliki, a następnie wybierz pozycję **Usuń.** Potwierdź, że chcesz usunąć replikę.

Aby użyć interfejsu wiersza polecenia platformy Azure do usunięcia repliki *myregistry* w regionie Wschodnie usa:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Cennik replikacji geograficznej

Replikacja geograficzna jest funkcją warstwy [usługi Premium usługi](container-registry-skus.md) Azure Container Registry. Replikacja rejestru w żądanych regionach wiąże się z naliczaniem opłat za rejestr w warstwie Premium w danym regionie.

W poprzednim przykładzie firma Contoso skonsolidowała dwa rejestry w ramach jednego, dodając repliki do regionu Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Firma Contoso zapłaciłaby miesięcznie czterokrotną opłatę za warstwę Premium bez dodatkowej konfiguracji ani zarządzania. W każdym regionie obrazy są teraz ściągane lokalnie, co zwiększa wydajność i niezawodność bez ponoszenia opłat za ruch wychodzący w sieci z regionu Zachodnie stany USA do regionu Kanada Środkowa i Wschodnie stany USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Rozwiązywanie problemów z operacjami wypychania przy użyciu rejestrów replikowanych geograficznie
 
Klient platformy Docker, który wypycha obraz do rejestru replikowanego geograficznie, może nie wypchnąć wszystkich warstw obrazu i jego manifestu do jednego zreplikowanego regionu. Może się to zdarzyć, Azure Traffic Manager kieruje żądania rejestru do zreplikowanego rejestru znajdującego się najbliżej sieci. Jeśli rejestr ma  dwa regiony replikacji w pobliżu, warstwy obrazu i manifest mogą być dystrybuowane do dwóch lokacji, a operacja wypychania kończy się niepowodzeniem po weryfikacji manifestu. Ten problem występuje ze względu na sposób rozpoznawania nazwy DNS rejestru na niektórych hostach z systemem Linux. Ten problem nie występuje w systemie Windows, który zapewnia pamięć podręczną DNS po stronie klienta.
 
W przypadku wystąpienia tego problemu jednym z rozwiązań jest zastosowanie pamięci podręcznej DNS po stronie klienta, takiej jak `dnsmasq` na hoście z systemem Linux. Pomaga to zagwarantować, że nazwa rejestru jest rozpoznawane w spójny sposób. Jeśli używasz maszyny wirtualnej z systemem Linux na platformie Azure do wypychania do rejestru, zobacz opcje w tesłudze DNS Name Resolution options for Linux virtual machines in Azure (Opcje rozpoznawania nazw DNS dla maszyn wirtualnych z systemem [Linux na platformie Azure).](../virtual-machines/linux/azure-dns.md)

Aby zoptymalizować rozpoznawanie nazw DNS do najbliższej repliki podczas wypychania obrazów, skonfiguruj rejestr z replikacją geograficzną w tych samych regionach świadczenia usługi Azure, w których znajduje się źródło operacji wypychania, lub najbliższy region podczas pracy poza platformą Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Tymczasowe wyłączenie routingu do replikacji

Aby rozwiązywać problemy z operacjami w rejestrze replikowanym geograficznie, warto tymczasowo wyłączyć routing Traffic Manager replikacji do co najmniej jednej replikacji. Począwszy od interfejsu wiersza polecenia platformy Azure w wersji 2.8, można skonfigurować opcję (wersja zapoznawcza) podczas tworzenia `--region-endpoint-enabled` lub aktualizowania replikowanego regionu. Jeśli opcja replikacji zostanie ustawiona na wartość , Traffic Manager do tego regionu nie będzie już przekierowyowywowy danych docker push ani `--region-endpoint-enabled` `false` pull requests. Domyślnie routing do wszystkich replikacji jest włączony, a synchronizacja danych między wszystkimi replikacjami odbywa się bez względu na to, czy routing jest włączony, czy wyłączony.

Aby wyłączyć routing do istniejącej replikacji, najpierw uruchom [az acr replication list,][az-acr-replication-list] aby wyświetlić listę replikacji w rejestrze. Następnie uruchom pozycję [az acr replication update i][az-acr-replication-update] ustaw dla `--region-endpoint-enabled false` określonej replikacji. Aby na przykład skonfigurować ustawienie replikacji *westus* w *myregistry*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Aby przywrócić routing do replikacji:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z trzyczęściową serią samouczków dostępną w sekcji [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md). Opisano w niej tworzenie rejestru z replikacją geograficzną, tworzenie kontenera, a następnie wdrażanie go w ramach wielu regionalnych aplikacji usługi Web Apps na potrzeby wystąpień kontenerów za pomocą pojedynczego polecenia `docker push`.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
