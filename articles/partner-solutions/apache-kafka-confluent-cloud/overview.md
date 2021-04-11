---
title: Apache Kafka na temat omówienia z chmurą w chmurze — rozwiązania partnerskie platformy Azure
description: Dowiedz się więcej o korzystaniu z Apache Kafka w chmurze w witrynie Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: fefbc21c385e3beacbf570c31ffbf97238c780fc
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109088"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Co to jest Apache Kafka dla usługi Cloud-The-Fluent?

Apache Kafka w przypadku usługi Cloud-The-Fluent to oferta portalu Azure Marketplace, która zapewnia Apache Kafka jako usługę. Jest ona w pełni zarządzana, dzięki czemu możesz skupić się na tworzeniu aplikacji, a nie zarządzaniu klastrami.

Aby zmniejszyć obciążenie związane z zarządzaniem na wielu platformach, firma Microsoft współpracuje z chmurą w celu utworzenia zintegrowanej warstwy aprowizacji z platformy Azure do współpracy w chmurze. Zapewnia ono skonsolidowane środowisko korzystania z chmury w chmurze na platformie Azure. Możesz łatwo zintegrować chmurę i zarządzać nią z aplikacjami platformy Azure.

Wcześniej trzeba było zakupić ofertę usługi Cloud-The-Fluent w portalu Marketplace i oddzielnie skonfigurować konto w usłudze w chmurze. Aby zarządzać konfiguracjami i zasobami, musisz przejść między portalami dla platformy Azure i z integracją z chmurą.

Teraz można zainicjować obsługę zasobów w chmurze przy użyciu dostawcy zasobów o nazwie **Microsoft.**.. Możesz tworzyć i zarządzać zasobami organizacji w chmurze, korzystając z [Azure Portal](https://portal.azure.com/), [interfejsu wiersza polecenia platformy Azure](/cli/azure/)lub [zestawów SDK platformy Azure](/azure/#languages-and-tools). Usługa w chmurze jest własnością i uruchamia aplikację oprogramowanie jako usługa (SaaS), w tym środowiska, klastry, tematy, klucze interfejsu API i łączniki zarządzane.

## <a name="capabilities"></a>Możliwości

Ścisła integracja między usługą i platformą Azure zapewnia następujące możliwości:

- Zainicjuj obsługę nowego zasobu organizacji z obsługą chmury, korzystając z Azure Portal z w pełni zarządzaną infrastrukturą.
- Usprawnij Logowanie jednokrotne (SSO) z platformy Azure, aby uzyskać chmurę z usługą Azure Active Directory (Azure AD). Portal z chmurą w chmurze nie wymaga oddzielnego uwierzytelniania.
- Korzystaj z ujednoliconego rozliczania korzyści związanych z chmurą za pomocą fakturowania subskrypcji platformy Azure.
- Zarządzaj zasobami w chmurze z poziomu Azure Portal i śledź je na stronie **wszystkie zasoby** przy użyciu innych zasobów platformy Azure.

## <a name="confluent-organization"></a>Organizacja z organizacją

Organizacja, której to dotyczy, jest zasobem, który zapewnia mapowanie między platformą Azure i zasobami w chmurze. Jest to zasób nadrzędny dla innych zasobów w chmurze.

Każda subskrypcja platformy Azure może zawierać wiele planów. Każdy plan jest mapowany na konto użytkownika i organizację w portalu z systemem. W ramach każdej organizacji, w której istnieje taka możliwość, można utworzyć wiele środowisk, klastrów, tematów i łączników.

Gdy inicjujesz zasób w chmurze z obsługą sieci na platformie Azure, uzyskasz identyfikator organizacji, domyślne środowisko i konto użytkownika. Aby uzyskać więcej informacji, zobacz [Szybki Start: Rozpoczynanie pracy z chmurą w systemie Azure](create.md).

W przypadku rozliczeń każda oferta w chmurze, która została zakupiona w witrynie Marketplace, jest mapowana na unikatową organizację.

## <a name="single-sign-on"></a>Logowanie jednokrotne

Po zalogowaniu się do Azure Portal poświadczenia są również używane do logowania się do portalu usługi Cloud SaaS w chmurze. Środowisko korzysta z [usługi Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) i [logowania jednokrotnego usługi Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md) w celu zapewnienia bezpiecznego i wygodnego sposobu logowania.

Aby uzyskać więcej informacji, zobacz [Logowanie jednokrotne](manage.md#single-sign-on).

## <a name="billing"></a>Rozliczenia

Dostępne są dwie opcje rozliczania: płatność zgodnie z rzeczywistym użyciem i plan zobowiązania.

- W przypadku **planu płatności zgodnie z rzeczywistym** użyciem możesz otrzymywać opłaty za użycie w chmurze na miesięcznym rachunku na korzystanie z platformy Azure.
- **Plan zobowiązania** polega na zarejestrowaniu się w celu uzyskania minimalnej ilości wydatków i uzyskaniu rabatu za zatwierdzone użycie usługi w chmurze.

Należy wybrać opcję rozliczania, która ma być używana podczas tworzenia usługi.

## <a name="connector-to-azure-cosmos-db"></a>Łącznik do Azure Cosmos DB

W ramach klienta usługi influenter Zainstaluj łącznik Cosmos DB zgodnie z zaleceniami na liście w [centrum](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos). 

Aby zainstalować łącznik ręcznie, należy najpierw pobrać plik JAR Uber na [stronie wydań Cosmos DB](https://github.com/microsoft/kafka-connect-cosmosdb/releases). Alternatywnie możesz [skompilować własny Uber jar bezpośrednio z kodu źródłowego](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Ukończ instalację, postępując zgodnie ze wskazówkami opisanymi w dokumentacji dotyczącej programu w celu [ręcznego instalowania łączników](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="confluent-links"></a>Linki z linkami

Aby uzyskać dodatkową pomoc dotyczącą korzystania z Apache Kafka dla usługi w chmurze, zobacz następujące linki do [witryny z witryną](https://docs.confluent.io/home/overview.html).

Aby dowiedzieć się więcej na temat opcji rozliczeń, zobacz:

* [Portal Azure Marketplace z opcją płatność zgodnie z rzeczywistym użyciem](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Portal Azure Marketplace z zobowiązaniami](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Aby dowiedzieć się więcej o zarządzaniu rozwiązaniami, zobacz:

* [Tworzenie klastra w chmurze z systemem](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Środowiska chmurowe w chmurze](https://docs.confluent.io/current/cloud/using/environments.html)
* [Podstawowe informacje o chmurze](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Aby uzyskać pomoc techniczną i warunki, zobacz:

* [Pomoc techniczna dotycząca poparcia](https://support.confluent.io)
* [Warunki użytkowania usługi](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Następne kroki

Aby utworzyć wystąpienie Apache Kafka dla usługi w chmurze, zobacz [Szybki Start: Rozpoczynanie pracy z chmurą w systemie Azure](create.md).
