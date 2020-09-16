# PaaS-TA 5.0 가이드 문서

## 123플랫폼 설치 가이드

* [설치 파일 다운로드 받기](https://paas-ta.kr/download/package)
* 운영 환경 설치
  * PaaS-TA 플랫폼 자동화 설치
    * [플랫폼 설치 자동화  설치](use-guide/platform/paas-ta_platform_install_automation_install_guide_v1.0.md)
    * [플랫폼 설치 자동화 사용 메뉴얼](use-guide/platform/paas-ta_platform_install_automation_use_manual_v1.0.md)
  * PaaS-TA 플랫폼 수동 설치
    * [BOSH 설치\(AWS, OpenStack, VMWare vSphere, GCP, MS Azure\)](install-guide/bosh/paas-ta_bosh2_install_guide_v5.0.md)
    * [PaaS-TA 수동 설치\(AWS, OpenStack, VMWare vSphere, GCP, MS Azure\)](install-guide/paasta/paas-ta_core_install_guide_v5.0.md)
    * [BOSH 및 PaaS-TA 설치\(CLOUDit\)](use-guide/platform/paas-ta_platform_install_automation_cloudit_v1.0.md)
    * [PaaS-TA 포털 수동 설치\(CLOUDit\)](use-guide/platform/paas-ta_platform_install_automation_cloudit_portal_v1.0.md)

## 가이드

* [CF Migration 가이드 \(3.1 to 4.0\)](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-4.0-ROTELLE/blob/master/PaaS_TA_4.0_migration.md)

## 포털 설치 가이드

* [PaaS-TA 포털 UI](install-guide/portal/paas-ta_portal_ui_service_install_guide_v1.0.md)
* [PaaS-TA 포털 API](install-guide/portal/paas-ta_portal_api_service_install_guide_v1.0.md)

## 서비스 설치 가이드

**아래 서비스 설치 전에 BOSH, PaaS-TA, PaaS-TA 포털이 설치되어 있어야 한다.**

* DBMS 설치
  * [Cubrid](service-guide/dbms/paas-ta_cubrid_service_install_guide_v1.0.md)
  * [MySQL](service-guide/dbms/paas-ta_mysql_service_install_guide_v1.0.md)
* NOSQL 설치
  * [MongoDB](service-guide/nosql/paas-ta_mongodb_service_install_guide_v1.0.md)
  * [Redis](service-guide/nosql/paas-ta_on_demand_redis_service_install_guide_v1.0.md)
* Storage 설치
  * [GlusterFS](service-guide/storage/paas-ta_glusterfs_service_install_guide_v1.0.md)
* MessageQueue 설치
  * [RabbitMQ](service-guide/messagequeue/paas-ta_rabbitmq_service_install_guide_v1.0.md)
* Web IDE 설치
  * [Web IDE](service-guide/webide/paas-ta_web_ide_install_guide_v1.0.md)
* Pinpoint APM 설치
  * [Pinpoint APM](service-guide/etc/paas-ta_pinpoint_service_install_guide_v1.0.md)  
* 통합 개발 도구 설치
  * [배포파이프라인](service-guide/tools/paas-ta_delivery_pipeline_service_install_guide_v1.0.md)
  * [형상관리](service-guide/tools/paas-ta_source_control_service_install_guide_v1.0.md)
  * [Container 서비스](service-guide/tools/paas-ta_container_service_install_guide_v2.0.md)
  * [Logging 서비스](service-guide/tools/paas-ta_logging_service_install_guide_v1.0.md)
  * [애플리케이션 Gateway 서비스](service-guide/tools/paas-ta_application_gateway_service_install_guide_v1.0.md)
  * [라이프사이클 관리 서비스](service-guide/tools/paas-ta_lifecycle_management_service_install_guide_v1.0.md)
* 미터링
  * [CF-Abacus](install-guide/metering/paas-ta_metering_install_guide.md)

## 마켓플레이스 설치 가이드

**마켓플레이스 설치 전에 BOSH, PaaS-TA, PaaS-TA 포털이 설치되어 있어야 한다.**

* [PaaS-TA 마켓플레이스](service-guide/marketplace/paas-ta_marketplace_install_guide_v1.0.md)

## 모니터링 설치 가이드

* [PaaS-TA 모니터링](service-guide/monitoring/paas-ta_monitoring_install_guide_v5.0.md)

## 활용 가이드

* [BOSH CLI V2\(Command Line Interface\) 사용](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-4.0-ROTELLE/blob/master/Use-Guide/Bosh/PaaS-TA_BOSH_CLI_V2_사용자_가이드v1.0.md)
* [CF CLI\(Command Line Interface\) 사용](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Use-Guide/OpenPaas%20CLi%20가이드.md)
* [Eclipse plugin 개발도구 사용](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Use-Guide/Open%20PaaS%20개발환경%20사용%20가이드.md)
* [PaaS-TA 사용자 포털 가이드](use-guide/portal/paas-ta_user_portal_use_guide_v1.1.md)
* [PaaS-TA 운영자 포털 가이드](use-guide/portal/paas-ta_admin_portal_use_guide_v1.1.md)
* [PaaS-TA 배포 파이프라인 사용자 가이드](use-guide/tools/paas-ta_delivery_pipeline_service_use_guide_v1.0.md)
* [PaaS-TA 형상관리 서비스 사용자 가이드](use-guide/tools/paas-ta_source_control_service_use_guide_v1.0.md)
* [PaaS-TA Container 서비스 사용자 가이드](use-guide/tools/paas-ta_container_service_use_guide_v2.0.md)
* [PaaS-TA Logging 서비스 사용자 가이드](use-guide/tools/paas-ta_logging_service_use_guide_v1.0.md)
* [PaaS-TA Jenkins 서비스](use-guide/tools/paas-ta_jenkins_service_user_guide.md)
* [PaaS-TA 마켓플레이스 가이드](use-guide/marketplace/paas-ta_marketplace_use_guide_v1.0.md)

  **개발 언어별 애플리케이션 가이드**

* [Node.js](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_Nodejs_develope_guide.md)
* [PHP](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_PHP_develope_guide.md)
* [Python](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_Python_develope_guide.md)
* [Ruby](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_Ruby_develope_guide.md)
* [Java](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_Java_develope_guide.md)
* [Go](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Sample-App-Guide/OpenPaaS_PaaSTA_Application_Go_develope_guide.md)

## 플랫폼 개발 가이드

* [스템셀 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Development-Guide/OpenPaaS_PaaSTA_Build_Stemcell_guide.md)
* [서비스팩 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Development-Guide/ServicePack_develope_guide.md)
* [빌드팩 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Development-Guide/Buildpack_develope_guide.md)
* [애플리케이션 APIPlatform 도로주소 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Development-Guide/Application_APIPlatform_dorojuso_devlope_guide.md)
* [퍼블릭 API 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-1.0-Spaghetti-/blob/master/Development-Guide/PublicAPI_devlope_guide.md)
* [Java API 서비스 미터링 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-2.0-Linguine-/blob/master/Development-Guide/PaaS-TA_Java_API_서비스_미터링_개발_가이드.md)
* [Java 서비스 미터링 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-2.0-Linguine-/blob/master/Development-Guide/PaaS-TA_Java_서비스_미터링_개발_가이드.md)
* [Nodejs API 서비스 미터링 개발 가이드](https://github.com/jhuhm135/Guide-5.0-Ravioli/tree/6f0a60ad3ae8b403e4762e0ecc8619b8829d4e1f/Guide-2.0-Linguine-/blob/master/Development-Guide/PaaS-TA_Node.js_API_미터링_개발_가이드.md)
* [On-Demand 서비스 개발 가이드](deployment-guide/on-demand/on_demand_deployment_guide.md)

