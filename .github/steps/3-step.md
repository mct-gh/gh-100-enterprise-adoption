## 3단계: 진단 정보를 남긴다

2단계에서 무엇을 붙일지 정했습니다. 이번에는 그것을 실제로 모읍니다.

워크플로가 API 로 리포 상태를 모아 파일로 만들고 **아티팩트**로 올립니다.
GHES 의 support bundle 을 GHEC 에서 대신하는 축소판입니다.

> [!NOTE]
> 아티팩트 자체를 깊게 배우려면 카탈로그 공식 랩
> [Work with Workflow Artifacts](https://github.com/skills/workflow-artifacts) 를 보세요.
> 단일 파일 업로드, 다운로드 후 배포, 워크플로 사이 전달, 승인 게이트까지 4단계로 다룹니다.
> 이 단계는 문법이 아니라 **무엇을 수집해 티켓에 붙이는가** 가 목적입니다.

### 할 일

1. `.github/workflows/diagnostics.yml` 파일을 만듭니다.

```yaml
name: Diagnostics

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  collect:
    runs-on: ubuntu-latest
    steps:
      - name: 리포 상태를 모은다
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          mkdir -p out
          gh api "repos/${GITHUB_REPOSITORY}" > out/repo.json
          gh api "repos/${GITHUB_REPOSITORY}/actions/workflows" > out/workflows.json
          echo "수집 시각 $(date -u)" > out/collected-at.txt

      - name: 아티팩트로 올린다
        uses: actions/upload-artifact@v7
        with:
          name: diagnostics
          path: out/
```

2. **Actions 탭 → Diagnostics → Run workflow** 로 한 번 실행합니다.
3. 실행이 끝나면 **Actions 탭 → Step 3 → Run workflow** 를 눌러 채점을 실행하세요.

<details>
<summary>아티팩트는 얼마나 남나요</summary><br/>

기본 보존 기간은 90일입니다. 조직이나 리포 설정에서 줄일 수 있습니다.
보존 기간이 지나면 증거가 사라지므로, 오래 보관해야 하는 것은 따로 내려받아 둡니다.

</details>

<details>
<summary>채점이 실패하나요</summary><br/>

- Diagnostics 워크플로를 실제로 실행했는지 확인하세요. 파일만 만들면 아티팩트가 생기지 않습니다
- 실행이 성공으로 끝났는지 확인하세요

</details>
