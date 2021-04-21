---
title: Integrowanie Azure App Configuration z wdrażaniem kubernetes przy użyciu programu Helm
description: Dowiedz się, jak używać konfiguracji dynamicznych we wdrożeniu kubernetes za pomocą programu Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 6276fc2027e92d5b7baaf9237a928e7828a3b021
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775771"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integracja z wdrożeniem Kubernetes przy użyciu programu Helm

Program Helm umożliwia definiowanie, instalowanie i uaktualnianie aplikacji działających na platformie Kubernetes. Pakiet Helm zawiera informacje niezbędne do utworzenia wystąpienia aplikacji Kubernetes. Konfiguracja jest przechowywana poza samym wykresem w pliku o nazwie *values.yaml*. 

Podczas procesu wydania program Helm scala pakiet z odpowiednią konfiguracją w celu uruchomienia aplikacji. Na przykład zmienne zdefiniowane w pliku *values.yaml* można przywoływać jako zmienne środowiskowe w uruchomionych kontenerach. Program Helm obsługuje również tworzenie wpisów tajnych kubernetes, które mogą być instalowane jako woluminy danych lub udostępniane jako zmienne środowiskowe.

Wartości przechowywane w pliku *values.yaml* można przesłonić, podając dodatkowe pliki konfiguracji oparte na pliku YAML w wierszu polecenia podczas uruchamiania programu Helm. Azure App Configuration obsługuje eksportowanie wartości konfiguracji do plików YAML. Zintegrowanie tej możliwości eksportu z wdrożeniem umożliwia aplikacjom Kubernetes wykorzystanie wartości konfiguracji przechowywanych w App Configuration.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Użyj wartości z App Configuration podczas wdrażania aplikacji na kubernetes przy użyciu programu Helm.
> * Utwórz klucz tajny Kubernetes na podstawie odwołania Key Vault w App Configuration.

W tym samouczku założono podstawową wiedzę na temat zarządzania usługą Kubernetes za pomocą programu Helm. Dowiedz się więcej o instalowaniu aplikacji za pomocą programu Helm w [Azure Kubernetes Service](../aks/kubernetes-helm.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalowanie [interfejsu wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure (wersja 2.4.0 lub nowsza)
- Zainstaluj [program Helm](https://helm.sh/docs/intro/install/) (wersja 2.14.0 lub nowsza)
- Klaster Kubernetes.

## <a name="create-an-app-configuration-store"></a>Tworzenie App Configuration magazynu

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Wybierz **pozycję Utwórz w programie Configuration Explorer,** aby dodać następujące pary  >   klucz-wartość:

    | Klucz | Wartość |
    |---|---|
    | settings.color | Biały |
    | settings.message | Dane z usługi Azure App Configuration |

    Na **razie pozostaw** **pola Etykieta i Typ** zawartości puste.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Dodawanie odwołania Key Vault do App Configuration
1. Zaloguj się do konta [Azure Portal](https://portal.azure.com) i dodaj klucz tajny, aby Key Vault [o](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) nazwie **Password** i wartości **myPassword.** 
2. Wybierz wystąpienie App Configuration magazynu utworzone w poprzedniej sekcji.

3. Wybierz **pozycję Configuration Explorer**.

4. Wybierz **pozycję + Utwórz** odwołanie do magazynu  >  **kluczy,** a następnie określ następujące wartości:
    - **Klucz:** wybierz **pozycję secrets.password.**
    - **Etykieta:** pozostaw tę wartość pustą.
    - **Subskrypcja,** **grupa zasobów** i **magazyn kluczy:** wprowadź wartości odpowiadające wartościom w magazynie kluczy utworzonym w poprzednim kroku.
    - **Klucz** tajny: wybierz klucz tajny o **nazwie Password** utworzony w poprzedniej sekcji.

## <a name="create-helm-chart"></a>Tworzenie wykresu helm ##
Najpierw utwórz przykładowy pakiet Helm za pomocą następującego polecenia
```console
helm create mychart
```

Program Helm tworzy nowy katalog o nazwie mychart o strukturze pokazanej poniżej. 

> [!TIP]
> Postępuj zgodnie z [tym przewodnikiem po wykresach,](https://helm.sh/docs/chart_template_guide/getting_started/) aby dowiedzieć się więcej.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Następnie zaktualizuj **sekcję spec:template:spec:containers** pliku *deployment.yaml.* Poniższy fragment kodu dodaje dwie zmienne środowiskowe do kontenera. Ich wartości ustawisz dynamicznie w czasie wdrażania.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Kompletny plik *deployment.yaml* po aktualizacji powinien wyglądać jak poniżej.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Aby przechowywać poufne dane jako wpisy tajne kubernetes, dodaj plik *secrets.yaml* w folderze templates.

> [!TIP]
> Dowiedz się więcej na temat używania [wpisów tajnych kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Na koniec zaktualizuj plik *values.yaml* przy użyciu następującej zawartości, aby opcjonalnie podać domyślne wartości ustawień konfiguracji i wpisów tajnych, do których odwołują się pliki *deployment.yaml* i *secrets.yaml.* Ich rzeczywiste wartości zostaną zastąpione przez konfigurację ściągniętą z App Configuration.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Pass configuration from App Configuration in Helm install (Przekaż konfigurację z App Configuration w instalacji programu Helm) ##
Najpierw pobierz konfigurację z witryny App Configuration do *pliku myConfig.yaml.* Użyj filtru klucza, aby pobrać tylko te klucze, które zaczynają się od **ustawień.**. Jeśli w Twoim przypadku filtr kluczy nie jest wystarczający do wykluczenia kluczy odwołań do Key Vault, możesz użyć argumentu **--skip-keyvault,** aby je wykluczyć. 

> [!TIP]
> Dowiedz się więcej o [poleceniu eksportu](/cli/azure/appconfig/kv#az_appconfig_kv_export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Następnie pobierz wpisy tajne do pliku o nazwie *mySecrets.yaml.* Argument wiersza polecenia **--resolve-keyvault** rozwiązuje odwołania Key Vault przez pobieranie rzeczywistych wartości w Key Vault. Musisz uruchomić to polecenie z poświadczeniami, które mają uprawnienia dostępu do odpowiednich Key Vault.

> [!WARNING]
> Ponieważ ten plik zawiera poufne informacje, zachowaj ostrożność i wyczyść plik, gdy nie będzie już potrzebny.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Użyj argumentu **-f** uaktualnienia programu Helm, aby przekazać dwa utworzone pliki konfiguracji. Zastąpią one wartości konfiguracji zdefiniowane w *pliku values.yaml* wartościami wyeksportowaymi z pliku App Configuration.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Możesz również użyć argumentu **--set** dla uaktualnienia programu Helm, aby przekazać wartości klucza literału. Użycie **argumentu --set** jest dobrym sposobem na uniknięcie utrwalania poufnych danych na dysku. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Sprawdź, czy konfiguracje i wpisy tajne zostały pomyślnie ustawione, korzystając z [pulpitu nawigacyjnego rozwiązania Kubernetes.](../aks/kubernetes-dashboard.md) Zobaczysz, że **wartości** kolorów  i komunikatów z App Configuration zostały wypełnione w zmiennych środowiskowych kontenera.

![Lokalne uruchamianie aplikacji z przewodnika Szybki start](./media/kubernetes-dashboard-env-variables.png)

Jeden wpis **tajny, hasło**, przechowuje jako Key Vault w App Configuration został również dodany do wpisów tajnych kubernetes. 

![Zrzut ekranu przedstawiający wyróżnienie hasła w sekcji Dane.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wyeksportowano dane Azure App Configuration, które mają być używane we wdrożeniu kubernetes za pomocą programu Helm. Aby dowiedzieć się więcej na temat korzystania z App Configuration, przejdź do przykładów interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Interfejs wiersza polecenia platformy Azure](/cli/azure/appconfig)
