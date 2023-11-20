# GitHub Actions Workflow 통과여부 뱃지 추가 방법

GitHub Actions를 이용해서 Workflow를 추가한 후에는 아래와 같이 Readme에 뱃지를 삽입할 수 있습니다.

![Alt text](./img/workflow_status_badge/status_badge_demo.png)

기본적인 양식은 아래와 같습니다.

```
https://github.com/사용자이름/저장소이름/actions/workflows/workflow파일이름.yml/badge.svg
```

이외에도 branch, event에 대한 status도 지정할 수 있습니다. 아직 무슨 목적으로 써야할지 파악을 못해서, 나중에 추가적으로 업데이트할 내용이 있으면 업데이트하겠습니다.

```
![example workflow](https://github.com/github/docs/actions/workflows/main.yml/badge.svg)

![example branch parameter](https://github.com/github/docs/actions/workflows/main.yml/badge.svg?branch=feature-1)

![example event parameter](https://github.com/github/docs/actions/workflows/main.yml/badge.svg?event=push)
```

아래는 제 프로젝트의 workflow에 대한 status badge 입니다.

<p align="center">
<img src="https://github.com/limvik/budget-management-service/actions/workflows/build-test-containerize-deploy.yml/badge.svg">
</p>

자세한 내용은 아래 공식 문서를 통해 확인하실 수 있습니다.

출처: [Adding a workflow status badge](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)