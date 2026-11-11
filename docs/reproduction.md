# 실행과 공개 범위

## 환경 설치

```text
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
```

CPU용 PyTorch 인덱스가 requirements에 지정돼 있다. 최종 CNN 경로에서 사용하는 torchvision을 추가했다. 사전학습 가중치가 캐시에 없으면 첫 특징 추출 때 다운로드한다. 이 공개본으로 새 환경 설치·전체 학습을 다시 실행하지는 않았다.

## 데이터 준비

원본 이미지는 포함하지 않는다. 사용 권한이 있는 자료를 `data/raw/<기종>/<제품>/step_N.jpg` 구조로 준비한다. NG는 해당 제품의 NG 폴더에 둔다. 체크리스트 Excel과 학습 아티팩트도 공개본에 없다.

## 기본 분석

```text
python -m scripts.build_inventory
python -m scripts.eda_full
python -m scripts.train --config configs/config_cnn.yaml --machine 3029C003AA --steps 2 9 14 --run demo --min-val 150 --max-train 60
python -m scripts.evaluate --config configs/config_cnn.yaml --machine 3029C003AA --steps 2 9 14 --run demo --n-synthetic 15
```

데이터를 준비한 뒤 프로젝트 루트에서 실행한다. 설정과 원본 데이터의 대응을 먼저 확인한다. 과거 결과와 새 결과는 별도 run 이름으로 관리한다.

## 후속 통합 코드

`src/inspection/registry.py`는 기종·스텝 규칙을 읽고, `router.py`는 존재·문자·범용 검사 어댑터를 선택하며, `decision.py`는 결과를 합친다. 미완성 C는 NOT_APPLICABLE이며 검증된 모듈로 취급하지 않는다.

```text
python -m scripts.build_inspection_rules
python -m scripts.infer_product --machine 3029C009AA --product <제품폴더명> --include-ng --verbose
python -m scripts.evaluate_integrated
```

이 명령들은 원본 사진뿐 아니라 각 모듈의 참조 사진·모델·임계값 아티팩트를 요구한다. 코드만 내려받아 즉시 6/12 결과를 재현할 수 있는 것은 아니다.

## 정리본 점검

Python 코드의 구문을 검사했다. 전처리·재학습·실제 불량 재평가는 수행하지 않았다. 원본 코드의 연구용 구현을 유지했으며, 운영 안전성·판정 규칙의 모든 경계조건을 검증한 배포용 시스템으로 표시하지 않는다.

원본·파생 공정 이미지, 제품별 상세 목록, 학번이 포함된 Word/PDF는 제외했다. `reports/submitted_report_text.md`는 제공받은 Word 보고서의 학번 제외 텍스트다. 원본 팀 코드와 후속 보고서의 출처는 SOURCE.md에 있다.
