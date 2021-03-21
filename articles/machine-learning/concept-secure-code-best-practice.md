---
title: Najlepsze rozwiązania zabezpieczające kod
titleSuffix: Azure Machine Learning
description: Poznaj potencjalne zagrożenia bezpieczeństwa, które mogą występować podczas opracowywania Azure Machine Learning, środków zaradczych i najlepszych rozwiązań.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322981"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Najlepsze rozwiązania w zakresie kodu z Azure Machine Learning

W Azure Machine Learning można przekazać pliki i zawartość z dowolnego źródła na platformę Azure. Zawartość w notesach Jupyter lub załadowane skrypty mogą potencjalnie odczytywać dane z sesji, uzyskiwać dostęp do danych w organizacji na platformie Azure lub uruchamiać złośliwe procesy w Twoim imieniu.

> [!IMPORTANT]
> Uruchamiać Notesy i skrypty z zaufanych źródeł. Na przykład, gdy ty lub Twój zespół ds. zabezpieczeń zaprzeglądał Notes lub skrypt.

## <a name="potential-threats"></a>Potencjalne zagrożenia

Programowanie za pomocą Azure Machine Learning często obejmuje środowiska deweloperskie oparte na sieci Web (notesy & Azure ML Studio). W przypadku korzystania ze środowisk deweloperskich opartych na sieci Web potencjalne zagrożenia są następujące:

* [Skrypty między lokacjami (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Iniekcja modelu dom__: ten typ ataku może zmodyfikować interfejs użytkownika wyświetlany w przeglądarce. Na przykład zmieniając sposób zachowania przycisku Uruchom w Jupyter Notebook.
    * __Token dostępu/pliki cookie__: ataki XSS mogą również uzyskiwać dostęp do plików cookie magazynu lokalnego i przeglądarki. Token uwierzytelniania usługi Azure Active Directory (AAD) jest przechowywany w magazynie lokalnym. Atak typu XSS może używać tego tokenu do wykonywania wywołań interfejsu API w Twoim imieniu, a następnie wysyłania danych do zewnętrznego systemu lub interfejsu API.

* [Fałszerstwo żądania między lokacjami (CSRF)](https://owasp.org/www-community/attacks/csrf): Ten atak może zastąpić adres URL obrazu lub połączyć się z adresem URL złośliwego skryptu lub interfejsu API. Po załadowaniu obrazu lub kliknięciu linku zostanie wykonane wywołanie do adresu URL.

## <a name="azure-ml-studio-notebooks"></a>Notesy usługi Azure ML Studio

Program Azure Machine Learning Studio udostępnia hostowane środowisko notesu w przeglądarce. Komórki w notesie mogą wyprowadzać dokumenty HTML lub fragmenty, które zawierają złośliwy kod.  Gdy dane wyjściowe są renderowane, kod może być wykonywany.

__Możliwe zagrożenia__:
* Skrypty między lokacjami (XSS)
* Fałszerstwo żądania między lokacjami (CSRF)

Środki __zaradcze zapewniane przez Azure Machine Learning__:
* __Dane wyjściowe komórki kodu__ są w trybie piaskownicy w elemencie iframe. Element IFRAME uniemożliwia skryptowi uzyskiwanie dostępu do nadrzędnego modelu DOM, plików cookie lub magazynu sesji.
* Zawartość __komórki o promocji__ jest czyszczona przy użyciu biblioteki dompurify. Powoduje to zablokowanie złośliwych skryptów wykonywanych przy użyciu komórek z promocjią.
* __Adres URL obrazu__ i __linki do promocji cenowych__ są wysyłane do należącego do punktu końcowego firmy Microsoft, który wyszukuje złośliwe wartości. W przypadku wykrycia złośliwej wartości punkt końcowy odrzuca żądanie.

__Zalecane akcje__:
* Upewnij się, że ufasz zawartości plików przed przekazaniem do Studio. Podczas przekazywania należy potwierdzić, że są przekazywane zaufane pliki.
* Po wybraniu linku umożliwiającego otwarcie aplikacji zewnętrznej zostanie wyświetlony monit o zaufać aplikacji.

## <a name="azure-ml-compute-instance"></a>Wystąpienie obliczeniowe platformy Azure ML

Wystąpienie obliczeniowe Azure Machine Learning hostuje __laboratorium__ __Jupyter__ i Jupyter. W przypadku używania obu komórek w notesie lub kodzie w programie można wyprowadzać dokumenty HTML lub fragmenty, które zawierają złośliwy kod. Gdy dane wyjściowe są renderowane, kod może być wykonywany. Te same zagrożenia mają zastosowanie również w przypadku używania __RStudio__ hostowanego w wystąpieniu obliczeniowym.

__Możliwe zagrożenia__:
* Skrypty między lokacjami (XSS)
* Fałszerstwo żądania między lokacjami (CSRF)

Środki __zaradcze zapewniane przez Azure Machine Learning__:
* Brak. Jupyter i Jupyter Lab to aplikacje Open Source hostowane w wystąpieniu obliczeniowym Azure Machine Learning.

__Zalecane akcje__:
* Upewnij się, że ufasz zawartości plików przed przekazaniem do Studio. Podczas przekazywania należy potwierdzić, że są przekazywane zaufane pliki.

## <a name="report-security-issues-or-concerns"></a>Zgłoś problemy z zabezpieczeniami lub wątpliwości 

Azure Machine Learning kwalifikuje się w programie Microsoft Azure bounty. Aby uzyskać więcej informacji, odwiedź stronę  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-enterprise-security.md)