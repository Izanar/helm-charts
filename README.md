# Mediapulse Helm Charts

Helm-чарты проекта [Mediapulse](https://github.com/Izanar/Mediapulse-Gitops):

- `backend` — API и Worker Mediapulse
- `databases` — PostgreSQL + Redis
- `argocd` — обёртка над `argo/argo-cd` (значения проекта + Traefik IngressRoute, upstream-чарт запакован внутрь)

Исходники в основном репозитории: [`Mediapulse-Gitops/local/charts`](https://github.com/Izanar/Mediapulse-Gitops/tree/main/local/charts). Этот репозиторий — **публикуемая копия**: при изменении чарта перенесите его сюда (см. ниже) и bump-ьте версию в `Chart.yaml`.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.

Once Helm has been set up correctly, add the repo as follows:

    helm repo add mediapulse https://izanar.github.io/helm-charts

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages. You can then run `helm search repo
mediapulse` to see the charts.

To install the `argocd` chart:

    helm install argocd mediapulse/argocd -n argocd

To install the `backend` chart:

    helm install mediapulse-backend mediapulse/backend -n default

To install the `databases` chart:

    helm install mediapulse-databases mediapulse/databases -n default

To uninstall a chart:

    helm uninstall <release-name> -n <namespace>

## Как публикуются чарты

Workflow [Release Charts](.github/workflows/release.yml) при пуше в `main` (с изменениями в `charts/**`) через [chart-releaser-action](https://github.com/helm/chart-releaser-action):

1. проверяет версии во всех `charts/*/Chart.yaml`;
2. для каждой **новой** версии создаёт GitHub Release с именем `<chart>-<version>` и прикладывает туда упакованный `.tgz`;
3. создаёт/обновляет `index.yaml` в ветке `gh-pages` — это и есть helm-репозиторий, отдаваемый GitHub Pages по `https://izanar.github.io/helm-charts`.

Повторный пуш той же версии ничего не делает (releases иммутабельны) — поэтому **изменение чарта всегда требует bump-а версии** в его `Chart.yaml`.

## Синхронизация с основным репозиторием

Исходники правятся в `Mediapulse-Gitops/local/charts/`. После изменения:

```bash
# скопировать изменённый чарт сюда
cp -a ../Mediapulse-Gitops/local/charts/<имя> charts/

# bump версии в charts/<имя>/Chart.yaml, затем:
git add charts/<имя> && git commit -m "release: <имя> <новая-версия>" && git push
```
