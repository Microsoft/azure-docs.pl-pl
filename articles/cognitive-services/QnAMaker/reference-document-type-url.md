---
title: Typy adresów URL obsługiwane przez QnA Maker importowania
description: Dowiedz się, jak typy adresów URL są używane do importowania i tworzenia par QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776701"
---
# <a name="urls-supported-for-importing-documents"></a>Adresy URL obsługiwane do importowania dokumentów

Dowiedz się, jak typy adresów URL są używane do importowania i tworzenia par QnA.

## <a name="faq-urls"></a>Adresy URL często zadawane pytania

QnA Maker mogą obsługiwać strony sieci Web często zadawane pytania w 3 różnych formach:

* Strony często zadawanych pytań
* Strony często zadawanych pytań z linkami
* Strony z często zadawanymi pytaniami z stroną główną tematów

### <a name="plain-faq-pages"></a>Strony często zadawanych pytań

Jest to najczęściej spotykany typ strony często zadawanych pytań, w której odpowiedzi są natychmiast zgodne z pytaniami na tej samej stronie.

Poniżej znajduje się przykład zwykłej strony często zadawanych pytań:

![Przykład strony często zadawanych pytań dotyczących bazy wiedzy](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Strony często zadawanych pytań z linkami

Na stronie ten typ często zadawanych pytań pytania są agregowane i połączone z odpowiedziami, które znajdują się w różnych sekcjach tej samej strony lub na różnych stronach.

Poniżej znajduje się przykład strony często zadawanych pytań z linkami w sekcjach, które znajdują się na tej samej stronie:

 ![Przykład strony z często zadawanymi pytaniami na temat bazy wiedzy](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Strony tematów nadrzędnych łącza do stron odpowiedzi podrzędnych

Ten typ często zadawanych pytań zawiera strony tematów, w których każdy temat jest połączony z odpowiednim zestawem pytań i odpowiedzi na innej stronie. QnA Maker przeszukuje wszystkie połączone strony w celu wyodrębnienia odpowiednich pytań & odpowiedzi.

Poniżej znajduje się przykład strony tematów z linkami do sekcji często zadawanych pytań na różnych stronach.

 ![Przykład strony często zadawanych pytań dotyczących linków dla bazy wiedzy](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Adresy URL pomocy technicznej

QnA Maker mogą przetwarzać strony sieci Web pomocy technicznej z częściową strukturą, takie jak artykuły w sieci Web, które opisują sposób wykonywania danego zadania, diagnozowania i rozwiązywania danego problemu oraz najważniejszych rozwiązań dla danego procesu. Wyodrębnianie działa najlepiej w przypadku zawartości, która ma jasno strukturę z nagłówkami hierarchicznymi.

> [!NOTE]
> Wyodrębnianie artykułów pomocy technicznej jest nową funkcją i znajduje się na wczesnych etapach. Najlepiej sprawdza się w przypadku prostych stron, które są dobrze strukturalne i nie zawierają złożonych nagłówków/stopek.

![QnA Maker obsługuje wyodrębnianie ze stron sieci Web z częściową strukturą, w przypadku których przejrzysta struktura jest prezentowana z nagłówkami hierarchicznymi](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)