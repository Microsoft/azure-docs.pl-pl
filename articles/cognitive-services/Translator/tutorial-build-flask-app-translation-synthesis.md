---
title: 'Samouczek: Tworzenie aplikacji do kolby do translacji, wytłumaczania i analizowania tłumaczeń tekstu'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację sieci Web opartą na kolbie, która umożliwia tłumaczenie tekstu, analizowanie tonacji i syntezę przetłumaczonego tekstu w mowę.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 6ec951e57b40ae1440f541c02b26e7788b3cf151
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043737"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Samouczek: Tworzenie aplikacji do kolby za pomocą usługi Azure Cognitive Services

W tym samouczku utworzysz nową aplikację sieci Web, która korzysta z usługi Azure Cognitive Services do tłumaczenia tekstu, analizowania tonacji i syntezowania przetłumaczonego tekstu na mowę. Nasz fokus znajduje się w oparciu o kod w języku Python i trasy kolb, które umożliwiają naszej aplikacji, ale pomożemy Ci w rozwiązaniu z kodem HTML i JavaScript, który pobiera aplikację razem. Jeśli napotkasz jakiekolwiek problemy, skontaktuj się z nami za pomocą przycisku opinii poniżej.

Oto, co obejmuje ten samouczek:

> [!div class="checklist"]
> * Pobierz klucze subskrypcji platformy Azure
> * Konfigurowanie środowiska deweloperskiego i Instalowanie zależności
> * Tworzenie aplikacji do kolby
> * Tłumaczenie tekstu przy użyciu translatora
> * Użyj analiza tekstu, aby analizować pozytywne/ujemne tonacji tekstu wejściowego i tłumaczenia
> * Konwertowanie przetłumaczonego tekstu na mowę z użyciem usługi Speech Services
> * Uruchamianie aplikacji z kolby lokalnie

> [!TIP]
> Jeśli chcesz pominąć i wyświetlić cały kod jednocześnie, cały przykład wraz z instrukcjami dotyczącymi kompilacji jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co to jest platforma Flask?

Kolba jest mikroplatformą do tworzenia aplikacji sieci Web. Oznacza to, że Kolba zawiera narzędzia, biblioteki i technologie umożliwiające tworzenie aplikacji sieci Web. Ta aplikacja sieci Web może być witrynami sieci Web, blogami, witrynami typu wiki lub w formie komercyjnej witryny internetowej.

W przypadku użytkowników, którzy chcą uzyskać głębokie szczegółowe po tym samouczku, są kilka przydatnych linków:

* [Dokumentacja dotycząca kolby](http://flask.pocoo.org/)
* [Kolba dla manekinów — Przewodnik początkującego do kolby](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z oprogramowaniem i kluczami subskrypcji, które będą potrzebne w tym samouczku.

* [Python 3,6 lub nowszy](https://www.python.org/downloads/)
* [Narzędzia Git](https://git-scm.com/downloads)
* IDE lub Edytor tekstu, taki jak [Visual Studio Code](https://code.visualstudio.com/) lub [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) lub [Firefox](https://www.mozilla.org/firefox)
* Klucz subskrypcji usługi **translator** (można raczej użyć lokalizacji **globalnej** ).
* Klucz subskrypcji **Analiza tekstu** w regionie **zachodnie stany USA** .
* Klucz subskrypcji **usługi Speech Services** w regionie **zachodnie stany USA** .

## <a name="create-an-account-and-subscribe-to-resources"></a>Tworzenie konta i subskrybowanie zasobów

Jak wspomniano wcześniej, będziesz potrzebować trzech kluczy subskrypcji dla tego samouczka. Oznacza to, że musisz utworzyć zasób na koncie platformy Azure dla:
* Translator
* Analiza tekstu
* Usługi mowy

Aby uzyskać instrukcje krok po kroku dotyczące tworzenia zasobów, użyj polecenia [Utwórz konto Cognitive Services w Azure Portal](../cognitive-services-apis-create-account.md) .

> [!IMPORTANT]
> Na potrzeby tego samouczka Utwórz zasoby w regionie zachodnie stany USA. W przypadku korzystania z innego regionu należy dostosować podstawowy adres URL w każdym z plików języka Python.

## <a name="set-up-your-dev-environment"></a>Konfigurowanie środowiska deweloperskiego

Przed utworzeniem kolby aplikacji sieci Web należy utworzyć katalog roboczy dla projektu i zainstalować kilka pakietów języka Python.

### <a name="create-a-working-directory"></a>Tworzenie katalogu roboczego

1. Otwórz wiersz polecenia (Windows) lub terminal (macOS/Linux). Następnie Utwórz katalog roboczy i podkatalogi dla projektu:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Przejdź do katalogu roboczego projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Tworzenie i aktywowanie środowiska wirtualnego za pomocą `virtualenv`

Utwórzmy środowisko wirtualne dla naszej aplikacji z przedziału `virtualenv` . Użycie środowiska wirtualnego gwarantuje, że masz czyste środowisko do pracy.

1. W katalogu roboczym Uruchom to polecenie, aby utworzyć środowisko wirtualne: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Jawnie zadeklarowano, że środowisko wirtualne powinno używać języka Python 3. Dzięki temu użytkownicy z wieloma instalacjami w języku Python używają poprawnej wersji.

   **Windows bash/Windows:**
   ```
   virtualenv venv
   ```
   Aby zachować prostotę, nazywamy venv środowiska wirtualnego.

2. Polecenia umożliwiające aktywację środowiska wirtualnego będą się różnić w zależności od platformy/powłoki:   

   | Platforma | Powłoka | Polecenie |
   |----------|-------|---------|
   | macOS/Linux | bash/ZSH | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Wiersz polecenia | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po uruchomieniu tego polecenia, w wierszu polecenia lub w sesji terminala powinien być poprzedzony `venv` .

3. Sesję można dezaktywować w dowolnym momencie, wpisując w wierszu polecenia lub terminalu: `deactivate` .

> [!NOTE]
> Język Python zawiera obszerną dokumentację tworzenia środowisk wirtualnych i zarządzania nimi, zobacz [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Żądania instalacji

Żądania to popularny moduł, który jest używany do wysyłania żądań HTTP 1,1. Nie ma potrzeby ręcznego dodawania ciągów zapytań do adresów URL ani do kodowania danych POST.

1. Aby zainstalować żądania, uruchom polecenie:

   ```
   pip install requests
   ```

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o żądaniach, zobacz temat [żądania: http dla ludzi](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Zainstaluj i skonfiguruj kolbę

Następnie musimy zainstalować kolbę. Kolba obsługuje routing dla naszej aplikacji sieci Web i umożliwia nam tworzenie wywołań serwer-serwer, które ukrywają nasze klucze subskrypcji od użytkownika końcowego.

1. Aby zainstalować kolbę, uruchom polecenie:
   ```
   pip install Flask
   ```
   Upewnijmy się, że Kolba została zainstalowana. Uruchom:
   ```
   flask --version
   ```
   Wersja powinna być drukowana do terminalu. Coś innego oznacza, że wystąpił problem.

2. Aby uruchomić aplikację z kolbą, możesz użyć polecenia kolby lub przełącznika języka Python-m z kolbą. Przed wykonaniem tej czynności należy poinformować Terminal, z którym aplikacja ma współpracować, eksportując `FLASK_APP` zmienną środowiskową:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Tworzenie aplikacji do kolby

W tej sekcji utworzysz aplikację podstawoweej kolby, która zwraca plik HTML, gdy użytkownicy trafią do katalogu głównego aplikacji. Nie poświęcaj zbyt dużo czasu na próbę pobrania kodu, wróćmy do aktualizacji tego pliku później.

### <a name="what-is-a-flask-route"></a>Co to jest trasa do kolby?

Poświęćmy chwilę na rozmowę o "[trasach](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Routing jest używany do powiązania adresu URL z określoną funkcją. Kolba używa trasy dekoratory do rejestrowania funkcji dla określonych adresów URL. Na przykład gdy użytkownik nawiguje do głównego ( `/` ) aplikacji sieci Web, `index.html` jest renderowany.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Spójrzmy na jeden przykład, aby wyrównać ten dom.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ten kod gwarantuje, że gdy użytkownik nawiguje do `http://your-web-app.com/about` `about.html` pliku.

Chociaż te przykłady ilustrują sposób renderowania stron HTML dla użytkownika, trasy mogą być również używane do wywoływania interfejsów API po naciśnięciu przycisku lub podjęcia dowolnej liczby akcji bez konieczności nawigowania po stronie głównej. Ta akcja zostanie wyświetlona podczas tworzenia tras do translacji, tonacji i syntezy mowy.

### <a name="get-started"></a>Rozpoczęcie pracy

1. Otwórz projekt w środowisku IDE, a następnie utwórz plik o nazwie `app.py` w katalogu głównym katalogu roboczego. Następnie skopiuj ten kod do `app.py` i Zapisz:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Ten blok kodu informuje aplikację `index.html` za każdym razem, gdy użytkownik przechodzi do katalogu głównego aplikacji sieci Web ( `/` ).

2. Następnie Utwórzmy fronton dla naszej aplikacji sieci Web. Utwórz plik o nazwie `index.html` w `templates` katalogu. Następnie skopiuj ten kod do programu `templates/index.html` .

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Przetestujmy aplikację do kolby. W terminalu uruchom polecenie:

   ```
   flask run
   ```

4. Otwórz przeglądarkę i przejdź do podanego adresu URL. Powinna zostać wyświetlona aplikacja jednostronicowa. Naciśnij **klawisze CTRL + C** , aby skasować aplikację.

## <a name="translate-text"></a>Tłumaczenie tekstu

Teraz, gdy masz pomysł na działanie prostej aplikacji do kolby, przyjrzyjmy się:

* Napisz część języka Python, aby wywołać translator i zwrócić odpowiedź
* Tworzenie trasy kolby do wywoływania kodu w języku Python
* Aktualizowanie kodu HTML za pomocą obszaru na potrzeby wprowadzania tekstu i tłumaczenia, selektor języka i przycisk tłumaczenie
* Napisz kod JavaScript, który pozwala użytkownikom na korzystanie z aplikacji z kolby w kodzie HTML

### <a name="call-the-translator"></a>Wywoływanie translatora

Najpierw należy napisać funkcję do wywołania translatora. Ta funkcja będzie przyjmować dwa argumenty: `text_input` i `language_output` . Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk tłumaczenie w aplikacji. Obszar tekstowy w kodzie HTML jest wysyłany jako `text_input` , a wartość wybór języka w kodzie HTML jest wysyłana jako `language_output` .

1. Zacznijmy od utworzenia pliku o nazwie `translate.py` w katalogu głównym katalogu roboczego.
2. Następnie Dodaj ten kod do `translate.py` . Ta funkcja przyjmuje dwa argumenty: `text_input` i `language_output` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   location = 'YOUR_TRANSLATOR_RESOURCE_LOCATION'
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': location,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Dodaj klucz subskrypcji usługi Translator i Zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do `app.py`

Następnie musisz utworzyć trasę w aplikacji kolby, która wywołuje `translate.py` . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk tłumaczenie w aplikacji.

W przypadku tej aplikacji trasy będą akceptować `POST` żądania. Wynika to z faktu, że funkcja oczekuje tekstu do przetłumaczenia i języka wyjściowego dla tłumaczenia.

Kolba zawiera funkcje pomocnika, które ułatwiają analizowanie poszczególnych żądań i zarządzanie nimi. W podanym kodzie `get_json()` zwraca dane z `POST` żądania jako JSON. Następnie za pomocą `data['text']` i `data['to']` , wartości języka text i Output są przekazywane do `get_translation()` funkcji dostępnej z `translate.py` . Ostatnim krokiem jest zwrócenie odpowiedzi w formacie JSON, ponieważ konieczne będzie wyświetlenie tych danych w aplikacji sieci Web.

W poniższych sekcjach należy powtórzyć ten proces podczas tworzenia tras dla analizy tonacji i syntezy mowy.

1. Otwórz `app.py` i Znajdź instrukcję import u góry `app.py` i Dodaj następujący wiersz:

   ```python
   import translate
   ```
   Teraz nasza aplikacja do kolby może korzystać z metody dostępnej za pośrednictwem `translate.py` .

2. Skopiuj ten kod na koniec `app.py` i Zapisz:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję tłumaczenia tekstu i trasy w aplikacji w kolbie do jej wywołania, następnym krokiem jest rozpoczęcie tworzenia kodu HTML dla aplikacji. Poniższy kod HTML wykonuje kilka czynności:

* Zapewnia obszar tekstu, w którym użytkownicy mogą wprowadzać tekst do przetłumaczenia.
* Zawiera selektor języka.
* Zawiera elementy HTML do renderowania wykrytego języka i wyników poufności zwróconych podczas tłumaczenia.
* Udostępnia obszar tekstu tylko do odczytu, w którym jest wyświetlane dane wyjściowe tłumaczenia.
* Zawiera symbole zastępcze dla kodu tonacji Analysis i syntezy mowy, które zostaną dodane do tego pliku w dalszej części tego samouczka.

Aktualizacjamy `index.html` .

1. Otwórz `index.html` i Znajdź następujące Komentarze do kodu:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Zastąp Komentarze do kodu tym blokiem HTML:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Następnym krokiem jest napisanie kodu JavaScript. Jest to mostek między trasą HTML i kolbą.

### <a name="create-mainjs"></a>Create `main.js`  

Ten `main.js` plik jest mostkiem między trasą HTML i kolbą. Twoja aplikacja będzie używać kombinacji jQuery, AJAX i XMLHttpRequest do renderowania zawartości i `POST` żądania do tras do kolb.

W poniższym kodzie zawartość kodu HTML służy do konstruowania żądania do trasy w kolbie. W odróżnieniu od zawartości obszaru tekstowego i selektora języka są przypisywane zmienne, a następnie przekazane w żądaniu do `translate-text` .

Kod następnie iteruje przez odpowiedź i aktualizuje kod HTML za pomocą tłumaczenia, wykrytego języka i wyniku pewności.

1. W środowisku IDE Utwórz plik o nazwie `main.js` w `static/scripts` katalogu.
2. Kopiuj ten kod do `static/scripts/main.js` :
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Testuj tłumaczenie

Przetestujmy tłumaczenie w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wejściowym, wybierz język i naciśnij pozycję Przekształć. Należy uzyskać tłumaczenie. Jeśli nie działa, upewnij się, że został dodany klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa w oczekiwany sposób, spróbuj usunąć pamięć podręczną lub otworzyć okno prywatne/incognito.

Naciśnij **klawisze CTRL + c** , aby skasować aplikację, a następnie przejdź do następnej sekcji.

## <a name="analyze-sentiment"></a>Analiza tonacji

[Interfejs API analizy tekstu](../text-analytics/overview.md) można użyć do przeprowadzenia analizy tonacji, wyodrębnienia kluczowych fraz z tekstu lub wykrycia języka źródłowego. W tej aplikacji będziemy używać analizy tonacji, aby określić, czy podany tekst jest dodatni, neutralny, czy ujemny. Interfejs API zwraca wynik liczbowy z zakresu od 0 do 1. Wyniki zbliżone do 1 oznaczają pozytywne tonacji, a wyniki zbliżone do 0 wskazują na ujemne tonacji.

W tej sekcji nastąpi kilka rzeczy:

* Napisz część języka Python, aby wywołać interfejs API analizy tekstu, aby przeprowadzić analizę tonacji i zwrócić odpowiedź
* Tworzenie trasy kolby do wywoływania kodu w języku Python
* Zaktualizuj kod HTML za pomocą obszaru dla wyników tonacji i przycisk, aby przeprowadzić analizę
* Napisz kod JavaScript, który pozwala użytkownikom na korzystanie z aplikacji z kolby w kodzie HTML

### <a name="call-the-text-analytics-api"></a>Wywoływanie interfejsu API usługi Text Analytics

Napiszmy funkcję wywołującą interfejs API analizy tekstu. Ta funkcja będzie przyjmować cztery argumenty: `input_text` , `input_language` , `output_text` i `output_language` . Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk Uruchom analizę tonacji w aplikacji. Dane dostarczone przez użytkownika z obszaru tekstowego i selektora języka, a także wykryty język i dane wyjściowe tłumaczenia są udostępniane każdemu żądaniu. Obiekt Response zawiera wyniki tonacji dla źródła i tłumaczenia. W poniższych sekcjach można napisać kod JavaScript, aby przeanalizować odpowiedź i użyć jej w aplikacji. Na razie skupmy się na interfejs API analizy tekstu.

1. Utwórzmy plik o nazwie `sentiment.py` w katalogu głównym katalogu roboczego.
2. Następnie Dodaj ten kod do `sentiment.py` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'
   endpoint = "YOUR_TEXT_ANALYTICS_ENDPOINT" 
   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language):
       path = '/text/analytics/v3.0/sentiment'
       constructed_url = endpoint + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Dodaj klucz subskrypcji analiza tekstu i Zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do `app.py`

Utwórzmy trasę w aplikacji kolby, która wywołuje `sentiment.py` . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk Uruchom analizę tonacji w aplikacji. Podobnie jak trasy do translacji, ta trasa ma akceptować `POST` żądania, ponieważ funkcja oczekuje argumentów.

1. Otwórz `app.py` i Znajdź instrukcję import u góry `app.py` i zaktualizuj ją:

   ```python
   import translate, sentiment
   ```
   Teraz nasza aplikacja do kolby może korzystać z metody dostępnej za pośrednictwem `sentiment.py` .

2. Skopiuj ten kod na koniec `app.py` i Zapisz:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję do uruchamiania analizy tonacji i trasy w aplikacji do kolby do jej wywołania, następnym krokiem jest rozpoczęcie pisania kodu HTML dla aplikacji. Poniższy kod HTML wykonuje kilka czynności:

* Dodaje przycisk do aplikacji w celu uruchomienia analizy tonacji
* Dodaje element, który objaśnia ocenianie tonacji
* Dodaje element, aby wyświetlić wyniki tonacji

1. Otwórz `index.html` i Znajdź następujące Komentarze do kodu:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Zastąp Komentarze do kodu tym blokiem HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment can be labeled as "positive", "negative", "neutral", or "mixed". </p>
      <strong>Sentiment label for input:</strong> <span id="input-sentiment"></span><br />
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizowanie pliku `main.js`

W poniższym kodzie zawartość kodu HTML służy do konstruowania żądania do trasy w kolbie. W odróżnieniu od zawartości obszaru tekstowego i selektora języka są przypisywane zmienne, a następnie przekazane do `sentiment-analysis` trasy.

Kod następnie iteruje przez odpowiedź i aktualizuje kod HTML za pomocą wyników tonacji.

1. W środowisku IDE Utwórz plik o nazwie `main.js` w `static` katalogu.

2. Kopiuj ten kod do `static/scripts/main.js` :
   ```javascript
   //Run sentiment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage};

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].sentiment;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== ''){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Testowanie analizy tonacji

Przetestujmy analizę tonacji w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wejściowym, wybierz język i naciśnij pozycję Przekształć. Należy uzyskać tłumaczenie. Następnie naciśnij przycisk Uruchom analizę tonacji. Powinny być widoczne dwa wyniki. Jeśli nie działa, upewnij się, że został dodany klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa w oczekiwany sposób, spróbuj usunąć pamięć podręczną lub otworzyć okno prywatne/incognito.

Naciśnij **klawisze CTRL + c** , aby skasować aplikację, a następnie przejdź do następnej sekcji.

## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

[Interfejs API zamiany tekstu na mowę](../speech-service/text-to-speech.md) umożliwia aplikacji Konwertowanie tekstu na rozpoznawanie mowy przez człowieka. Usługa obsługuje standardowe, neuronowych i niestandardowe głosy. Nasza przykładowa aplikacja używa kilku dostępnych głosów, aby zapoznać się z pełną listą, zobacz [obsługiwane języki](../speech-service/language-support.md#text-to-speech).

W tej sekcji nastąpi kilka rzeczy:

* Napisz część języka Python, aby skonwertować zamianę tekstu na mowę za pomocą interfejsu API zamiany tekstu na mowę
* Tworzenie trasy kolby do wywoływania kodu w języku Python
* Zaktualizuj kod HTML za pomocą przycisku, aby przekonwertować tekst na mowę i element na potrzeby odtwarzania dźwięku
* Napisz kod JavaScript, który pozwala użytkownikom na korzystanie z aplikacji do przedziału

### <a name="call-the-text-to-speech-api"></a>Wywołaj tekst do Speech API

Napiszmy funkcję do konwersji zamiany tekstu na mowę. Ta funkcja będzie przyjmować dwa argumenty: `input_text` i `voice_font` . Ta funkcja jest wywoływana za każdym razem, gdy użytkownik naciśnie przycisk Konwertuj tekst na mowę w aplikacji. `input_text` czy dane wyjściowe tłumaczenia zwracane przez wywołanie do tłumaczenia tekstu, `voice_font` to wartość z selektora czcionki głosowej w kodzie HTML.

1. Utwórzmy plik o nazwie `synthesize.py` w katalogu głównym katalogu roboczego.

2. Następnie Dodaj ten kod do `synthesize.py` .
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Dodaj klucz subskrypcji usługi mowy i Zapisz.

### <a name="add-a-route-to-apppy"></a>Dodawanie trasy do `app.py`

Utwórzmy trasę w aplikacji kolby, która wywołuje `synthesize.py` . Ta trasa będzie wywoływana za każdym razem, gdy użytkownik naciśnie przycisk Konwertuj tekst na mowę w aplikacji. Podobnie jak trasy do tłumaczenia i analizy tonacji, ta trasa akceptuje `POST` żądania, ponieważ funkcja oczekuje dwóch argumentów: tekst do syntezy oraz czcionkę głosu do odtwarzania.

1. Otwórz `app.py` i Znajdź instrukcję import u góry `app.py` i zaktualizuj ją:

   ```python
   import translate, sentiment, synthesize
   ```
   Teraz nasza aplikacja do kolby może korzystać z metody dostępnej za pośrednictwem `synthesize.py` .

2. Skopiuj ten kod na koniec `app.py` i Zapisz:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Aktualizowanie pliku `index.html`

Teraz, gdy masz funkcję konwersji zamiany tekstu na mowę i trasy w aplikacji w kolbie do jej wywołania, następnym krokiem jest rozpoczęcie pisania kodu HTML dla aplikacji. Poniższy kod HTML wykonuje kilka czynności:

* Zawiera listę rozwijaną wyboru głosu
* Dodaje przycisk do konwersji zamiany tekstu na mowę
* Dodaje element audio, który jest używany do odtwarzania wygranej mowy

1. Otwórz `index.html` i Znajdź następujące Komentarze do kodu:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Zastąp Komentarze do kodu tym blokiem HTML:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Następnie odszukaj następujące Komentarze do kodu:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Zastąp Komentarze do kodu tym blokiem HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Upewnij się, że Zapisano swoją służbę.

### <a name="update-mainjs"></a>Aktualizowanie pliku `main.js`

W poniższym kodzie zawartość kodu HTML służy do konstruowania żądania do trasy w kolbie. W przypadku tłumaczenia i czcionki głosowej są przypisywane do zmiennych, a następnie przekazane do `text-to-speech` trasy.

Kod następnie iteruje przez odpowiedź i aktualizuje kod HTML za pomocą wyników tonacji.

1. W środowisku IDE Utwórz plik o nazwie `main.js` w `static` katalogu.
2. Kopiuj ten kod do `static/scripts/main.js` :
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. To już prawie koniec. W ostatnim przypadku należy dodać jakiś kod, aby `main.js` automatycznie wybrać czcionkę głosową opartą na języku wybranym do tłumaczenia. Dodaj ten blok kodu do `main.js` :
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testowanie aplikacji

Przetestujmy funkcję syntezy mowy w aplikacji.

```
flask run
```

Przejdź do podanego adresu serwera. Wpisz tekst w obszarze wejściowym, wybierz język i naciśnij pozycję Przekształć. Należy uzyskać tłumaczenie. Następnie wybierz głos, a następnie naciśnij przycisk Konwertuj tekst na mowę. tłumaczenie powinno być odtwarzane jako narzędzie do rozpoznawania mowy. Jeśli nie działa, upewnij się, że został dodany klucz subskrypcji.

> [!TIP]
> Jeśli wprowadzone zmiany nie są wyświetlane lub aplikacja nie działa w oczekiwany sposób, spróbuj usunąć pamięć podręczną lub otworzyć okno prywatne/incognito.

Oznacza to, że masz działającą aplikację, która wykonuje tłumaczenia, analizuje tonacji i tłumaczy mowę. Naciśnij **klawisze CTRL + c** , aby skasować aplikację. Upewnij się, że zapoznaj się z innymi [Cognitive Services platformy Azure](../index.yml).

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Kod źródłowy dla tego projektu jest dostępny w serwisie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi translator](./reference/v3-0-reference.md)
* [Dokumentacja interfejsu API analizy tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Dokumentacja interfejsu API zamiany tekstu na mowę](../speech-service/rest-text-to-speech.md)
