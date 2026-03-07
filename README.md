# Data Engineering Pipeline Portfolio (AWS)

실시간 이벤트 수집부터 개인정보 비식별화, 데이터 레이크 ETL, 이상/위험 상황 탐지까지 구성한 AWS 기반 데이터 엔지니어링 프로젝트입니다.

## Project Summary

- Ingestion: `API Gateway -> Lambda -> Kinesis Firehose -> S3(raw)`
- Privacy: Firehose Transform Lambda에서 사용자/앱 토큰화 및 위치정보 정밀도 축소
- Batch ETL: AWS Glue(Spark)로 `Raw -> Silver -> Gold` 계층 처리
- Detection Pipeline: Athena + Lambda 조합으로 위험 사용자/친구 관계 기반 위치 분석

## Architecture

1. 클라이언트 이벤트를 API Gateway로 수신
2. Lambda에서 payload 정규화 후 Firehose로 전송
3. Firehose Transform Lambda에서 토큰화/개인정보 처리
4. S3 Raw 저장 후 Glue Job으로 Silver 정제
5. Silver 데이터를 Gold 피처로 집계
6. Athena 쿼리와 Lambda 파이프라인으로 critical 대상 추출/분석

## Tech Stack

- AWS: API Gateway, Lambda, Kinesis Firehose, S3, Glue, Athena, DynamoDB
- Data Processing: PySpark, SQL
- Language: Python

## Repository Structure

- `api_gateway_firehose_lambda.ipynb`
  - API Gateway 이벤트를 수집해 표준 포맷으로 변환하고 Firehose로 적재
- `firehose_transform_lambda_tokenization.ipynb`
  - `_user_id`, `package`를 토큰으로 치환하고 위치정보 정밀도를 축소하는 Transform Lambda
- `silver_etl_glue_job.ipynb`
  - Raw 데이터를 전일 기준으로 읽어 스키마 정규화/품질검증 후 Silver 레이어로 저장
- `glue_silver_to_gold_etl.ipynb`
  - Silver 이벤트를 사용자 단위 Gold 피처로 집계
- `lambda1_critical_query.ipynb`
  - Athena 쿼리 기반 critical 대상 토큰 추출
- `lambda2_critical_pipeline.ipynb`
  - 대상 사용자와 친구 관계를 결합해 최신 위치/위험 정보 파이프라인 구성

## Key Engineering Points

- 개인정보 보호를 위한 토큰 매핑(`DynamoDB`) 및 재사용 로직
- 데이터 레이크 계층화(Raw/Silver/Gold)로 파이프라인 책임 분리
- Athena 기반 탐지 로직으로 분석 질의와 운영 파이프라인 연결
- 함수 단위 모듈화와 예외 처리로 Lambda 운영 안정성 확보

## Outcome

- 이벤트 수집부터 분석용 피처 생성, 이상 징후 탐지까지 단일 흐름으로 구현
- 개인정보 보호 요구사항과 분석 활용성을 동시에 고려한 데이터 플랫폼 설계

## Notes

- 현재 저장소는 노트북 기반 코드 예제 중심입니다.
- 실제 운영 시 IAM 권한, Glue Job 파라미터, S3 경로, Athena DB/테이블명을 환경에 맞게 조정해야 합니다.
