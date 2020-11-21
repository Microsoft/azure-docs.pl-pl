---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015505"
---
W tym przewodniku szybki start przedstawiono podstawowe wzorce projektowe dla rozpoznawanie osoby mówiącej przy użyciu zestawu Speech SDK, w tym:

* Weryfikacja niezależna od tekstu i tekstu
* Identyfikacja osoby mówiącej, aby identyfikować przykład głosu między grupą głosów
* Usuwanie profilów głosowych

Aby zapoznać się z pojęciami dotyczącymi rozpoznawania mowy, zobacz artykuł z [omówieniem](../../../speaker-recognition-overview.md) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznawanie osoby mówiącej jest obecnie obsługiwana *tylko* w zasobach usługi Azure Speech utworzonych w `westus` regionie.

## <a name="text-dependent-verification"></a>Weryfikacja zależna od tekstu

Weryfikacja osoby mówiącej to czynność potwierdzająca, że prelegent pasuje do znanego lub **zarejestrowanego** głosu. Pierwszym krokiem jest **zarejestrowanie** profilu głosowego, dzięki czemu usługa ma coś do porównania przyszłych przykładów głosowych. W tym przykładzie należy zarejestrować profil przy użyciu strategii **zależnej od tekstu** , która wymaga określonego hasła do rejestracji i weryfikacji. Zapoznaj [się z](/rest/api/speakerrecognition/) listą obsługiwanych hasła.

Zacznij od [utworzenia profilu głosowego](/rest/api/speakerrecognition/verification/textdependent/createprofile). W każdym z poleceń zacinanych w tym artykule należy wstawić swój klucz subskrypcji usługi rozpoznawania mowy i region.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Należy zauważyć, że istnieją trzy typy profilu głosowego:

- Weryfikacja zależna od tekstu
- Weryfikacja niezależna od tekstu
- Identyfikacja niezależna od tekstu

W tym przypadku utworzysz profil głosu weryfikacji zależnej od tekstu. Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Następnie [zarejestrujesz profil głosowy](/rest/api/speakerrecognition/verification/textdependent/createenrollment). W polu `--data-binary` wartość parametru Określ plik audio na komputerze, który zawiera jeden z obsługiwanych hasła, na przykład "My Voice to My Passport. Możesz zarejestrować taki plik dźwiękowy za pomocą aplikacji, takiej jak [rejestratora głosowego systemu Windows](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab), lub można go wygenerować przy użyciu [zamiany tekstu na mowę](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Ta odpowiedź informuje o konieczności zarejestrowania dwóch dodatkowych próbek audio.

Po zarejestrowaniu łącznej liczby trzech próbek audio należy otrzymać poniższą odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Teraz wszystko jest gotowe do [zweryfikowania próbki audio w profilu głosowy](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Ten przykład audio powinien zawierać takie samo hasło jak Przykłady użyte do zarejestrowania profilu głosowego.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

Oznacza to, że `Accept` hasło zostało dopasowane i weryfikacja zakończyła się pomyślnie. Odpowiedź zawiera również wynik podobieństwa z zakresu od 0.0 do 1,0.

Aby zakończyć, [Usuń profil głosowy](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Brak odpowiedzi.

## <a name="text-independent-verification"></a>Weryfikacja niezależna od tekstu

W przeciwieństwie do weryfikacji **zależnej od tekstu** , weryfikacja **niezależna od tekstu** :

* Nie wymaga wymawiania określonego hasła, co może być wypowiadane
* Nie wymaga trzech próbek audio *, ale wymaga* 20 s całkowitego dźwięku

Zacznij od [utworzenia profilu weryfikacji niezależnego od tekstu](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Następnie [zarejestruj profil głosowy](/rest/api/speakerrecognition/verification/textindependent/createenrollment). Zamiast przesyłać trzy próbki audio, należy przesłać próbki audio zawierające łącznie 20 sekund audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Po przesłaniu wystarczającej ilości próbek audio należy otrzymać poniższą odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Teraz wszystko jest gotowe do [zweryfikowania próbki audio w profilu głosowy](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Ten przykład audio nie musi zawierać hasła. Może zawierać dowolną mowę, o ile zawiera ona łącznie co najmniej cztery sekundy audio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

Oznacza to, że `Accept` weryfikacja zakończyła się pomyślnie. Odpowiedź zawiera również wynik podobieństwa z zakresu od 0.0 do 1,0.

Aby zakończyć, [Usuń profil głosowy](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Brak odpowiedzi.

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Identyfikacja osoby mówiącej służy do określenia **, kto** jest w danej grupie zarejestrowanych głosów. Proces jest podobny do **weryfikacji niezależnej od tekstu**, z główną różnicą, że można zweryfikować wiele profilów głosowych jednocześnie, zamiast weryfikować się w przypadku pojedynczego profilu.

Zacznij od [utworzenia profilu identyfikacji niezależnego od tekstu](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Następnie [zarejestrujesz profil głosowy](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Ponownie należy przesłać próbki audio zawierające łącznie 20 sekund audio. Te przykłady nie muszą zawierać hasła.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Po przesłaniu wystarczającej ilości próbek audio należy otrzymać poniższą odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Teraz możesz przystąpić do [identyfikowania próbki audio przy użyciu profilu głosowego](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Polecenie Identyfikuj akceptuje rozdzielaną przecinkami listę możliwych identyfikatorów profilów głosowych. W takim przypadku wystarczy przekazać identyfikator utworzonego wcześniej profilu głosu. Jeśli jednak chcesz, możesz przekazać wiele identyfikatorów, w których każdy profil głosowy jest rejestrowany przy użyciu próbek audio z innego głosu.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Powinna zostać wyświetlona następująca odpowiedź.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Odpowiedź zawiera identyfikator profilu głosowego, który najlepiej pasuje do przesłanej próbki audio. Zawiera również listę profilów głosowych kandydujących, które są klasyfikowane w kolejności podobieństwa.

Aby zakończyć, [Usuń profil głosowy](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Brak odpowiedzi.