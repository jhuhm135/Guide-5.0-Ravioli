# PAAS-TA\_LOGGING\_SERVICE\_INSTALL\_GUIDE\_V1.0

### Table of Contents

[1. 문서 개요](paas-ta_logging_service_install_guide_v1.0.md#1)

* [1.1. 목적](paas-ta_logging_service_install_guide_v1.0.md#1.1)
* [1.2. 범위](paas-ta_logging_service_install_guide_v1.0.md#1.2)
* [1.3. 시스템 구성](paas-ta_logging_service_install_guide_v1.0.md#1.3)
* [1.4. 참고자료](paas-ta_logging_service_install_guide_v1.0.md#1.4)

[2. Logging 서비스 설치](paas-ta_logging_service_install_guide_v1.0.md#2)

* [2.1. 설치 전 준비 사항](paas-ta_logging_service_install_guide_v1.0.md#2.1)
* [2.1.1. Logging 서비스 설치 파일 다운로드](paas-ta_logging_service_install_guide_v1.0.md#2.1.1)
* [2.1.2. Stemcell 다운로드](paas-ta_logging_service_install_guide_v1.0.md#2.1.2)
* [2.2. Logging 서비스 릴리즈 업로드](paas-ta_logging_service_install_guide_v1.0.md#2.2)
* [2.3. Logging 서비스 Deployment 파일 수정 및 배포](paas-ta_logging_service_install_guide_v1.0.md#2.3)

[3. Logging 서비스 관리](paas-ta_logging_service_install_guide_v1.0.md#3)

* [3.1. Logging 서비스 UAA Client 등록](paas-ta_logging_service_install_guide_v1.0.md#3.1)
* [3.2. Logging 서비스 사용 활성화](paas-ta_logging_service_install_guide_v1.0.md#3.2)

### 1. 문서 개요

#### 1.1. 목적

본 문서는 Logging 서비스 Release를 Bosh2.0을 이용하여 설치 하는 방법을 기술하였다.

#### 1.2. 범위

설치 범위는 Logging 서비스 Release를 검증하기 위한 기본 설치를 기준으로 작성하였다.

#### 1.3. 시스템 구성

본 장에서는 Logging 서비스의 시스템 구성에 대해 기술하였다. Logging 서비스 시스템은 Router, Collector, Queue, Parser, Elasticsearch, Visualization의 최소사항을 구성하였다.  
![](../../.gitbook/assets/image001%20%282%29.png)

| VM명 | 인스턴스 수 | vCPU수 | 메모리\(GB\) | 디스크\(GB\) |
| :--- | :---: | :---: | :---: | :--- |
| Router | 1 | 1 | 1 | Root 8G |
| Collector | 1 | 1 | 2 | Root 10G |
| Queue | 1 | 1 | 2 | Root 10G + Persistent disk 10G |
| Parser | N | 1 | 2 | Root 10G |
| Elasticsearch Master | 1 | 1 | 2 | Root 10G + Persistent disk 10G |
| Elasticsearch Data | N | 2 | 4 | Root 20G + Persistent disk 30G |
| Visualization | 1 | 1 | 2 | Root 10G |
| maintenance | 1 | 1 | 1 | Root 8G |

#### 1.4. 참고자료

> [http://bosh.io/docs](http://bosh.io/docs)  
> [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)

### 2. Logging 서비스 설치

#### 2.1. 설치 전 준비 사항

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. Logging 서비스 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0, PaaS-TA 포털이 설치되어 있어야 한다.

※ 설치 전 확인 사항

uaac client에 "firehose-to-syslog"가 등록되어 있는지 확인 하여, 등록되어 있는 경우에는 "authorities"를 확인하여 "cloud\_controller.admin" 권한을 부여한다.

```text
# endpoint 설정
$ uaac target https://uaa.<DOMAIN> --skip-ssl-validation

# target 확인
$ uaac target
Target: https://uaa.<DOMAIN>
Context: uaa_admin, from client uaa_admin

# uaac 로그인
$ uaac token client get <UAA_ADMIN_CLIENT_ID> -s <UAA_ADMIN_CLIENT_SECRET>

# "firehose-to-syslog" uaac client 확인
$ uaac client get firehose-to-syslog
scope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read
    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.write
client_id: firehose-to-syslog
resource_ids: none
authorized_grant_types: client_credentials
autoapprove:
authorities: uaa.none doppler.firehose                   >>>>>>>>  cloud_controller.admin 권한 여부 확인
lastmodified: 1552530293656

# "firehose-to-syslog" uaac client 변경
$ uaac client update firehose-to-syslog --authorities "doppler.firehose, uaa.none, cloud_controller.admin"

# "firehose-to-syslog" uaac client 확인
$ uaac client get firehose-to-syslog
scope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read
    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.write
client_id: firehose-to-syslog
resource_ids: none
authorized_grant_types: client_credentials
autoapprove:
authorities: uaa.none doppler.firehose cloud_controller.admin
lastmodified: 1552530293656
```

#### 2.1.1 Logging 서비스 설치 파일 다운로드

Logging 서비스 설치에 필요한 Deployment 및 릴리즈 파일을 다운로드 받아 서비스 설치 작업 경로로 위치시킨다.

* 설치 파일 다운로드 위치 : [https://paas-ta.kr/download/package](https://paas-ta.kr/download/package) = Deployment : paasta-logging-service = 릴리즈 파일 : paasta-logging-service-release.tgz
* 설치 작업 경로 생성 및 파일 다운로드

```text
# Deployment 다운로드 파일 위치 경로 생성
$ mkdir -p ~/workspace/paasta-5.0/deployment/service-deployment

# Deployment 다운로드(paasta-logging-service) 및 파일 경로 확인
$ ls ~/workspace/paasta-5.0/deployment/service-deployment/paasta-logging-service
logging-service-deploy.sh  manifests

# 릴리즈 다운로드 파일 위치 경로 생성
$ mkdir -p ~/workspace/paasta-5.0/release/service

# 릴리즈 파일 다운로드(paasta-logging-service-release.tgz) 및 파일 경로 확인
$ ls ~/workspace/paasta-5.0/release/service
paasta-logging-service-release.tgz
```

#### 2.1.2 Stemcell 다운로드

Logging 서비스 설치에 필요한 Stemcell을 확인하여 존재하지 않을 경우 BOSH 설치 가이드 문서를 참고 하여 Stemcell을 업로드 한다.

* 설치 파일 다운로드 위치 : [https://paas-ta.kr/download/package](https://paas-ta.kr/download/package)

  \`\`\`

  **Stemcell 목록 확인**

  $ bosh -e micro-bosh stemcells

  Using environment '10.30.40.111' as client 'admin'

Name Version OS CPI CID  
bosh-openstack-kvm-ubuntu-xenial-go\_agent 315.64\* ubuntu-xenial - a2d704b0-2768-4e55-84a6-4f3b1311e6f9

\(\*\) Currently deployed

1 stemcells

Succeeded

```text
### <div id="2.2"/> 2.2. Logging 서비스 릴리즈 업로드

- 릴리즈 목록을 확인하여 Logging 서비스 릴리즈(paasta-logging-service-release)가 업로드 되어 있지 않은 것을 확인한다.
```

## 릴리즈 목록 확인

$ bosh -e micro-bosh releases Using environment '10.30.40.111' as client 'admin'

Name Version Commit Hash  
binary-buildpack 1.0.32 _2399a07  
... \(\(생략\)\) ... paasta-container-service-projects-release 1.0_ ced4610+  
php-buildpack 4.3.77 _ca96e60  
postgres 38_ b4926da  
pxc 0.18.0 _acdf39f  
python-buildpack 1.6.34_ e7b7e15  
r-buildpack 1.0.10 _a9a0a9f  
routing 0.188.0_ db449e4  
ruby-buildpack 1.7.40 _fa9e7c5  
silk 2.23.0_ cdb44d5  
staticfile-buildpack 1.4.43 _aeef141  
statsd-injector 1.10.0_ b81ab23  
uaa 72.0\* 804589c

\(\*\) Currently deployed \(+\) Uncommitted changes

39 releases

Succeeded

```text
- Logging 서비스 릴리즈 파일을 업로드한다.
```

## 릴리즈 파일 업로드

$ bosh -e micro-bosh upload-release ~/workspace/paasta-5.0/release/service/paasta-logging-service-release.tgz Using environment '10.30.40.111' as client 'admin'

**\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\# 100.00% 91.26 MiB/s 4s**

Task 249

Task 249 \| 05:16:30 \| Extracting release: Extracting release \(00:00:02\) Task 249 \| 05:16:32 \| Verifying manifest: Verifying manifest \(00:00:00\) Task 249 \| 05:16:32 \| Resolving package dependencies: Resolving package dependencies \(00:00:00\) Task 249 \| 05:16:32 \| Creating new packages: cerebro/dd5b9a85982129a7e24fd8679c9a09fdac0857d0a749a4ff20ba3be6a6f585e8 \(00:00:01\) Task 249 \| 05:16:33 \| Creating new packages: curator/abf8864d97cc79aba00c6a4c6e51e2b10da2f36219ce358b65840e113e9cd652 \(00:00:00\) Task 249 \| 05:16:33 \| Creating new packages: elasticsearch/f3d584d6e084af4d8427795d0df96027cc6da3c4147c62714adcfd08abb33bc3 \(00:00:01\) Task 249 \| 05:16:34 \| Creating new packages: firehose-to-syslog/691fe09a5f9f130103b47b7c7a286ca0e53d8264198c7711dc431383ebe6afd7 \(00:00:00\) Task 249 \| 05:16:34 \| Creating new packages: golang/f45d2a9623fdac6b1a4a63625ce4a5c435342a5a95920335dd8ce4277be92dc5 \(00:00:02\) Task 249 \| 05:16:36 \| Creating new packages: haproxy/b9189484ff6f4d87259e299f9771998abe4d0ffd0a7dd21d941c6c7cc7d8b58a \(00:00:00\) Task 249 \| 05:16:36 \| Creating new packages: java8/5074349fa4922efb30893f466c10f7c1e6fcdd8eb66e89f9a8395110dad0ccc1 \(00:00:00\) Task 249 \| 05:16:36 \| Creating new packages: kibana/b57105c1860939340eb46391170d1ed59e3af3b6d9aea3e5592c4ead5b9a296d \(00:00:01\) Task 249 \| 05:16:37 \| Creating new packages: logsearch-config/aaf9f56bc5044625e7fa3fd790ee8f6ff1ca90cba17561ac0b1f4f7e3488cc1b \(00:00:00\) Task 249 \| 05:16:37 \| Creating new packages: logstash/4a3979c58c3a67a79b731de1cf7e4fb27dbcdb27c149d4af3c4d51be11c3aeee \(00:00:02\) Task 249 \| 05:16:39 \| Creating new packages: nats\_to\_syslog/87900e9a2c72f55d7ab346d7819184e21338f2d9b5ff285bcdfb8210b303bbe1 \(00:00:00\) Task 249 \| 05:16:39 \| Creating new packages: python3/0543e5945b39f81cfa9f70d70b94fe253321676d029ded40a093addfecbdf18a \(00:00:01\) Task 249 \| 05:16:40 \| Creating new packages: redis/a8d2d5161542bd05fcfaffd14f26b221549d75c3ac0f795d3941dd530b27c0a2 \(00:00:00\) Task 249 \| 05:16:40 \| Creating new packages: ruby2.3/829cbedc82964dd880f3323d5386680ce0eba6bbd8ab9b84eca2d1902f6d2d7d \(00:00:00\) Task 249 \| 05:16:40 \| Creating new jobs: archiver/30264a742c6c457f93804f6466ff1aa0a9b57a0d2b61098e22551ed3e4f22a64 \(00:00:00\) Task 249 \| 05:16:40 \| Creating new jobs: cerebro/fd675cbb04d1ada7f530187ea53ec1bd2fa45a542ef983a78164ad58d8263913 \(00:00:00\) Task 249 \| 05:16:40 \| Creating new jobs: curator/f551e893b31b1841e7636c95727415d85cf80931179f954231c27fd74abe96f0 \(00:00:00\) Task 249 \| 05:16:40 \| Creating new jobs: elasticsearch/cd7b7dfe4d9157e9807c7f5332e880e1d056c18214ea6817db8ca0338eb470bd \(00:00:00\) Task 249 \| 05:16:40 \| Creating new jobs: elasticsearch\_config/7a9fa8a6bffa7d537918612b814011824200a6b115b2eea84bcadd1cf56ca595 \(00:00:01\) Task 249 \| 05:16:41 \| Creating new jobs: enable\_shard\_allocation/23e6639e8309d510341cadb91829a2ed98d5ccbfc55ef6084fc5d59acb2617e7 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: haproxy/811e63ee59ac888d6da188cc2710ff1e18486310ac9e7f1fef6aef08fc4d3804 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: ingestor\_archiver/345644551d21ae3bf8ef86ea33d3ebbf233cad43ee368254dc566295cb2cddcf \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: ingestor\_cloudfoundry-firehose/eede8116a5acdbf19001401f67af15ed81291ebd5f7896857e85b1492387ce1b \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: ingestor\_syslog/fc73b8814f4a954be796139a1dc894e0446b0fa1f173e7ab000b886cc3566b49 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: kibana/b45b9667621a32d5d617981dd60b6cb2450b7fca4c3ce5eb214c17ec510b3be8 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: nats\_to\_syslog/dc68081f021ab82eab2a40a398e1a2f6577f096fd27e451ddeb166a70b391a10 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: parser/3deeea0dc25e6ad0d3f479f69ec8c5b33fe10f720ad54436eb2fd5f98dfda396 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: queue/f201ef1fc4c2a2bd6e463df30c549a276947b47589a48a281ef6ead1c744d494 \(00:00:00\) Task 249 \| 05:16:41 \| Creating new jobs: smoke\_tests/646ea14c38a12da55a5002b8655645673545afd20f592c7d54032dab2e961e67 \(00:00:00\) Task 249 \| 05:16:41 \| Release has been created: paasta-logging-service-release/1.0 \(00:00:00\)

Task 249 Started Thu Nov 21 05:16:30 UTC 2019 Task 249 Finished Thu Nov 21 05:16:41 UTC 2019 Task 249 Duration 00:00:11 Task 249 done

Succeeded

```text
- 릴리즈 목록을 확인하여 Logging 서비스 릴리즈(paasta-logging-service-release)가 업로드 되어 있는 것을 확인한다.
```

## 릴리즈 목록 확인

$ bosh -e micro-bosh releases Using environment '10.30.40.111' as client 'admin'

Name Version Commit Hash  
binary-buildpack 1.0.32 _2399a07  
... \(\(생략\)\) ... paasta-logging-service-release 1.0 4e24281+  
php-buildpack 4.3.77_ ca96e60  
postgres 38 _b4926da  
pxc 0.18.0_ acdf39f  
python-buildpack 1.6.34 _e7b7e15  
r-buildpack 1.0.10_ a9a0a9f  
routing 0.188.0 _db449e4  
ruby-buildpack 1.7.40_ fa9e7c5  
silk 2.23.0 _cdb44d5  
staticfile-buildpack 1.4.43_ aeef141  
statsd-injector 1.10.0 _b81ab23  
uaa 72.0_ 804589c

\(\*\) Currently deployed \(+\) Uncommitted changes

40 releases

Succeeded

```text
### <div id="2.3"/> 2.3. Logging 서비스 Deployment 파일 수정 및 배포

BOSH Deployment manifest는 Components 요소 및 배포의 속성을 정의한 YAML 파일이다.
Deployment 파일에서 사용하는 network, vm_type, disk_type 등은 Cloud config를 활용하고, 활용 방법은 BOSH 2.0 가이드를 참고한다.

-    Cloud config 설정 내용을 확인한다.
```

## Cloud config 조회

$ bosh -e micro-bosh cloud-config Using environment '10.30.40.111' as client 'admin'

azs:

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

        resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z1

      ... \(\(생략\)\) ...

      compilation:

      az: z1

      network: default

      reuse\_compilation\_vms: true

      vm\_type: large

      workers: 5

      disk\_types:

* disk\_size: 1024

  name: default

* disk\_size: 1024

  name: 1GB

  ... \(\(생략\)\) ...

  networks:

* name: default

  subnets:

  * azs:
    * z1
    * z2
    * z3
    * z4
    * z5
    * z6

      cloud\_properties:

      name: Internal

      dns:

    * 8.8.8.8

      gateway: 10.30.20.23

      range: 10.30.0.0/16

      reserved:

    * 10.30.0.0 - 10.30.111.40

      ... \(\(생략\)\) ...

      vm\_types:

* cloud\_properties:

    cpu: 1

    disk: 8192

    ram: 1024

  name: minimal

  ... \(\(생략\)\) ...

Succeeded

```text
- Deployment YAML에서 사용하는 변수들을 서버 환경에 맞게 수정한다.
```

## 변수 설정

$ vi ~/workspace/paasta-5.0/deployment/service-deployment/paasta-logging-service/manifests/vars.yml

## RELEASE

logging\_service\_release\_name: "paasta-logging-service-release" logging\_service\_release\_version: "1.0"

## STEMCELL

stemcell\_os: "ubuntu-xenial" stemcell\_version: "latest"

## VM\_TYPE

vm\_type\_minimal: "minimal" vm\_type\_default: "default" vm\_type\_medium: "medium"

## NETWORK

private\_network\_name: "service\_private" public\_network\_name: "service\_public" private\_nat\_network\_name: "default" \# AWS의 경우 nat network

## ELASTICSEARCH\_MASTER

es\_master\_azs: \[z5\] es\_master\_instances: 1 es\_master\_persistent\_disk\_type: "10GB" es\_master\_private\_ips: \["10.30.107.135"\] es\_master\_private\_url: "10.30.107.135"

## QUEUE

queue\_azs: \[z5\] queue\_instances: 1 queue\_persistent\_disk\_type: "10GB" queue\_private\_ips: \["10.30.107.139"\] queue\_private\_url: "10.30.107.139"

## MAINTENANCE

maintenance\_azs: \[z5\] maintenance\_instances: 1 maintenance\_private\_ips: \["10.30.107.136"\]

## ELASTICSEARCH\_DATA

es\_data\_azs: \[z5\] es\_data\_instances: 2 es\_data\_persistent\_disk\_type: "20GB" es\_data\_private\_ips: \["10.30.107.133", "10.30.107.134"\]

## VISUALIZATION

visualization\_azs: \[z5\] visualization\_instances: 1 visualization\_private\_ips: \["10.30.107.143"\] visualization\_version: "5.3.0"

## COLLECTOR

collector\_azs: \[z5\] collector\_instances: 1 collector\_private\_ips: \["10.30.107.131"\]

## PARSER

parser\_azs: \[z5\] parser\_instances: 2 parser\_private\_ips: \["10.30.107.137", "10.30.107.138"\] parser\_es\_index: "%{\[@metadata\]\[index\]}-%{+YYYY.MM.dd.HH}" parser\_es\_index\_type: '%{\[@metadata\]\[type\]}'

## ROUTER

router\_azs: \[z5\] router\_instances: 1 router\_private\_ips: \["10.30.107.140"\] router\_public\_ips: "115.68.47.181" router\_private\_url: "10.30.107.140"

## CF

cf\_client\_id : "laasclient" cf\_client\_secret : "clientsecret” system\_domain: ""

## LOGGING SERVICE

es\_config\_index\_prefix: "laas-" retention\_period: 7 laas\_logo: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAALQAAABGCAYAAABll74gAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA3FpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTQyIDc5LjE2MDkyNCwgMjAxNy8wNy8xMy0wMTowNjozOSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6MkRBNUQ0REMyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6MkRBNUQ0REIyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIChXaW5kb3dzKSI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOjQ5YzFjOTUyLWE2MDEtZTI0NC1iMzIzLTMzYTIyNzI1NDcxYiIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiLz4gPC9y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"

```text
-    Deploy 스크립트 파일을 서버 환경에 맞게 수정한다.  
  = vSphere : -o manifests/ops-files/vsphere-network.yml  
  = AWS : -o manifests/ops-files/aws-network.yml  
  = OpenStack : -o manifests/ops-files/openstack-network.yml  
  = Azure : -o manifests/ops-files/azure-network.yml  
  = GCP : -o manifests/ops-files/gcp-network.yml
```

## Deploy 스크립트 수정

$ vi ~/workspace/paasta-5.0/deployment/service-deployment/paasta-logging-service/logging-service-deploy.sh

## !/bin/bash

## SET VARIABLES

export LOGGING\_SERVICE\_DEPLOYMENT\_NAME='paasta-logging-service' export BOSH2\_NAME='micro-bosh'

bosh -e ${BOSH2\_NAME} -d ${LOGGING\_SERVICE\_DEPLOYMENT\_NAME} deploy --no-redact manifests/paasta\_logging\_service.yml  -l manifests/vars.yml  -o manifests/ops-files/vsphere-network.yml  -v uaa\_admin\_client\_id=  -v uaa\_admin\_client\_secret=

```text
- Logging 서비스를 배포한다.
```

## Logging 서비스 Deploy \(e.g. vSphere\)

$ cd ~/workspace/paasta-5.0/deployment/service-deployment/paasta-logging-service $ sh logging-service-deploy.sh Using environment '10.30.40.111' as client 'admin'

Using deployment 'paasta-logging-service'

* azs:
* * cloud\_properties:
* datacenters:
* * clusters:
* * BD-HA:
* resource\_pool: CF\_BOSH2\_Pool
* name: BD-HA
* name: z1
* * cloud\_properties:
* datacenters:
* * clusters:
* * BD-HA:
* resource\_pool: CF\_BOSH2\_Pool
* name: BD-HA
* name: z2

... \(\(생략\)\) ...

* compilation:
* az: z1
* network: default
* reuse\_compilation\_vms: true
* vm\_type: large
* workers: 5
* networks:
* * name: default
* subnets:
* * azs:
* * z1
* * z2
* * z3
* * z4
* * z5
* * z6
* cloud\_properties:
* name: Internal
* dns:
* * 8.8.8.8
* gateway: 10.30.20.23
* range: 10.30.0.0/16
* reserved:
* * 10.30.0.0 - 10.30.111.40

... \(\(생략\)\) ...

* disk\_types:
* * disk\_size: 1024
* name: default
* * disk\_size: 1024
* name: 1GB
* * disk\_size: 2048
* name: 2GB
* * disk\_size: 4096
* name: 4GB
* * disk\_size: 5120
* name: 5GB
* * disk\_size: 8192
* name: 8GB
* * disk\_size: 10240
* name: 10GB
* * disk\_size: 20480
* name: 20GB
* * disk\_size: 30720
* name: 30GB
* * disk\_size: 51200
* name: 50GB
* * disk\_size: 102400
* name: 100GB
* * disk\_size: 1048576
* name: 1TB
* stemcells:
* * alias: default
* os: ubuntu-xenial
* version: '315.64'
* releases:
* * name: paasta-logging-service-release
* version: '1.0'
* update:
* canaries: 1
* canary\_watch\_time: 30000-600000
* max\_in\_flight: 1
* serial: false
* update\_watch\_time: 5000-600000

... \(\(생략\)\) ...

* instance\_groups:
* * azs:
* * z5
* instances: 1
* jobs:
* * name: elasticsearch
* properties:
* elasticsearch:
* cluster\_name: ""
* exec: ""
* master\_hosts:
* * ""
* node:
* allow\_data: ""
* allow\_master: ""
* release: paasta-logging-service-release
* * name: cerebro
* properties:
* elasticsearch:
* cluster\_name: ""
* release: paasta-logging-service-release
* name: elasticsearch\_master
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.135
* persistent\_disk\_type: 10GB
* stemcell: default
* update:
* max\_in\_flight: 1
* vm\_type: default
* * azs:
* * z5
* instances: 1
* jobs:
* * name: queue
* properties:
* redis:
* host: ""
* release: paasta-logging-service-release
* name: queue
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.139
* persistent\_disk\_type: 10GB
* stemcell: default
* vm\_type: default
* * azs:
* * z5
* instances: 1
* jobs:
* * name: elasticsearch\_config
* properties:
* elasticsearch\_config:
* elasticsearch:
* host: ""
* index\_prefix: ""
* templates:
* * shards-and-replicas: ""
* * index-settings: ""
* * index-mappings: ""
* * index-mappings-laas: ""
* * index-mappings-app: ""
* * index-mappings-platform: ""
* release: paasta-logging-service-release
* * name: curator
* properties:
* curator:
* elasticsearch:
* host: ""
* port: ""
* purge\_logs:
* retention\_period: ""
* unit: ""
* elasticsearch\_config:
* index\_prefix: ""
* release: paasta-logging-service-release
* name: maintenance
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.136
* stemcell: default
* update:
* serial: true
* vm\_type: minimal
* * azs:
* * z5
* instances: 2
* jobs:
* * name: elasticsearch
* properties:
* elasticsearch:
* cluster\_name: ""
* exec: ""
* master\_hosts:
* * ""
* node:
* allow\_data: ""
* allow\_master: ""
* release: paasta-logging-service-release
* name: elasticsearch\_data
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.133
* * 10.30.107.134
* persistent\_disk\_type: 20GB
* stemcell: default
* update:
* max\_in\_flight: 1
* serial: true
* vm\_type: medium
* * azs:
* * z5
* instances: 1
* jobs:
* * name: kibana
* properties:
* kibana:
* elasticsearch:
* host: ""
* port: ""
* version: ""
* laas:
* cf\_api\_url: ""
* cf\_client\_id: ""
* cf\_client\_secret: ""
* cf\_uaa\_callback\_url: ""
* cf\_uaa\_scope: cloud\_controller.read&openid
* cf\_uaa\_url: ""
* elasticsearch\_index\_prefix: ""
* top\_left\_logo\_image: ""
* release: paasta-logging-service-release
* name: visualization
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.143
* stemcell: default
* vm\_type: default
* * azs:
* * z5
* instances: 1
* jobs:
* * name: ingestor\_syslog
* properties:
* logstash\_ingestor:
* debug: ""
* relp:
* port: ""
* redis:
* host: ""
* release: paasta-logging-service-release
* * name: ingestor\_cloudfoundry-firehose
* properties:
* cloudfoundry:
* api\_endpoint: ""
* firehose\_client\_id: ""
* firehose\_client\_secret: ""
* firehose\_events: ""
* skip\_ssl\_validation: ""
* create-uaa-client:
* cloudfoundry:
* system\_domain: ""
* uaa\_admin\_client\_id: ""
* uaa\_admin\_client\_secret: ""
* syslog:
* host: ""
* port: ""
* release: paasta-logging-service-release
* name: collector
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.131
* stemcell: default
* vm\_type: default
* * azs:
* * z5
* instances: 2
* jobs:
* * name: parser
* properties:
* logstash\_parser:
* debug: ""
* elasticsearch:
* index: ""
* index\_type: ""
* redis:
* host: ""
* release: paasta-logging-service-release
* * name: elasticsearch
* properties:
* elasticsearch:
* cluster\_name: ""
* exec: ""
* master\_hosts:
* * ""
* release: paasta-logging-service-release
* name: parser
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.137
* * 10.30.107.138
* stemcell: default
* update:
* max\_in\_flight: 4
* serial: false
* vm\_type: default
* * azs:
* * z5
* instances: 1
* jobs:
* * name: haproxy
* properties:
* haproxy:
* cluster\_monitor: ""
* ingestor:
* backend\_servers:
* * ""
* kibana:
* backend\_servers:
* * ""
* release: paasta-logging-service-release
* name: router
* networks:
* * name: service\_private
* static\_ips:
* * 10.30.107.140
* * default:
* * dns
* * gateway
* name: service\_public
* static\_ips: 115.68.47.181
* stemcell: default
* vm\_type: minimal
* name: paasta-logging-service

Continue? \[yN\]: y

Task 68414

Task 68414 \| 02:06:49 \| Preparing deployment: Preparing deployment \(00:00:02\) Task 68414 \| 02:06:54 \| Preparing package compilation: Finding packages to compile \(00:00:00\) Task 68414 \| 02:06:54 \| Compiling packages: haproxy/1d59aa8ae61da532ade4e9f5129428bbc101be64 Task 68414 \| 02:06:54 \| Compiling packages: kibana/4bf52faddc3486d0f81857ddba1d5a617e52cc5b Task 68414 \| 02:06:54 \| Compiling packages: python3/2a00051b1a0f02d70453c75e9036d41cb960ebfc Task 68414 \| 02:06:54 \| Compiling packages: golang/fb3379fa5b3afb2e6e515657a33f2145ba4bd076 Task 68414 \| 02:06:54 \| Compiling packages: ruby2.3/613589acad7ddafc74fd12316e3d9cf5346f612e Task 68414 \| 02:09:51 \| Compiling packages: kibana/4bf52faddc3486d0f81857ddba1d5a617e52cc5b \(00:02:57\) Task 68414 \| 02:09:51 \| Compiling packages: redis/ff3e314387f91116dd8906cd656ecd0476b4c7b5 Task 68414 \| 02:10:00 \| Compiling packages: golang/fb3379fa5b3afb2e6e515657a33f2145ba4bd076 \(00:03:06\) Task 68414 \| 02:10:00 \| Compiling packages: cerebro/278996853981243e3b921d18e255d55d998dcc7f Task 68414 \| 02:10:11 \| Compiling packages: haproxy/1d59aa8ae61da532ade4e9f5129428bbc101be64 \(00:03:17\) Task 68414 \| 02:10:11 \| Compiling packages: java8/ae41eecf1175fb16c678940d4d6e31af10405b6e Task 68414 \| 02:10:30 \| Compiling packages: cerebro/278996853981243e3b921d18e255d55d998dcc7f \(00:00:30\) Task 68414 \| 02:10:30 \| Compiling packages: elasticsearch/cb154811849156850a88023c3dd1fd46698a09da Task 68414 \| 02:10:33 \| Compiling packages: redis/ff3e314387f91116dd8906cd656ecd0476b4c7b5 \(00:00:42\) Task 68414 \| 02:10:33 \| Compiling packages: firehose-to-syslog/57480e93d19cefa2f26dfa787419d378b2104b13 Task 68414 \| 02:10:47 \| Compiling packages: java8/ae41eecf1175fb16c678940d4d6e31af10405b6e \(00:00:36\) Task 68414 \| 02:10:47 \| Compiling packages: logstash/a5bc4ac7fb0f561db9b9e03833510b866236af8e Task 68414 \| 02:10:57 \| Compiling packages: elasticsearch/cb154811849156850a88023c3dd1fd46698a09da \(00:00:27\) Task 68414 \| 02:11:09 \| Compiling packages: firehose-to-syslog/57480e93d19cefa2f26dfa787419d378b2104b13 \(00:00:36\) Task 68414 \| 02:12:09 \| Compiling packages: python3/2a00051b1a0f02d70453c75e9036d41cb960ebfc \(00:05:15\) Task 68414 \| 02:12:09 \| Compiling packages: curator/27ee549a38ebcef009bb0e63ac716d544dc8caa2 Task 68414 \| 02:12:16 \| Compiling packages: logstash/a5bc4ac7fb0f561db9b9e03833510b866236af8e \(00:01:29\) Task 68414 \| 02:12:48 \| Compiling packages: curator/27ee549a38ebcef009bb0e63ac716d544dc8caa2 \(00:00:39\) Task 68414 \| 02:16:41 \| Compiling packages: ruby2.3/613589acad7ddafc74fd12316e3d9cf5346f612e \(00:09:47\) Task 68414 \| 02:16:41 \| Compiling packages: logsearch-config/4cd3b3efcb7ea642badd77ff3b5189ae1d324734 \(00:00:21\) Task 68414 \| 02:17:50 \| Creating missing vms: elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: elasticsearch\_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: elasticsearch\_data/fa38698e-913c-4296-aac8-c0b56c84a71e \(1\) Task 68414 \| 02:17:50 \| Creating missing vms: collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 \(0\) Task 68414 \| 02:17:50 \| Creating missing vms: parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa \(1\) Task 68414 \| 02:17:50 \| Creating missing vms: router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 \(0\) Task 68414 \| 02:21:00 \| Creating missing vms: visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a \(0\) \(00:03:10\) Task 68414 \| 02:21:07 \| Creating missing vms: elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 \(0\) \(00:03:17\) Task 68414 \| 02:21:20 \| Creating missing vms: parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 \(0\) \(00:03:30\) Task 68414 \| 02:21:20 \| Creating missing vms: elasticsearch\_data/fa38698e-913c-4296-aac8-c0b56c84a71e \(1\) \(00:03:30\) Task 68414 \| 02:21:22 \| Creating missing vms: queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf \(0\) \(00:03:32\) Task 68414 \| 02:21:22 \| Creating missing vms: elasticsearch\_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece \(0\) \(00:03:32\) Task 68414 \| 02:21:24 \| Creating missing vms: collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 \(0\) \(00:03:34\) Task 68414 \| 02:21:24 \| Creating missing vms: maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc \(0\) \(00:03:34\) Task 68414 \| 02:21:24 \| Creating missing vms: router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 \(0\) \(00:03:34\) Task 68414 \| 02:21:25 \| Creating missing vms: parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa \(1\) \(00:03:35\) Task 68414 \| 02:21:28 \| Updating instance elasticsearch\_master: elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 \(0\) \(canary\) Task 68414 \| 02:21:28 \| Updating instance queue: queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf \(0\) \(canary\) Task 68414 \| 02:25:25 \| Updating instance elasticsearch\_master: elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 \(0\) \(canary\) \(00:03:57 Task 68414 \| 02:25:43 \| Updating instance queue: queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf \(0\) \(canary\) \(00:04:15\) Task 68414 \| 02:25:43 \| Updating instance maintenance: maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc \(0\) \(canary\) \(00:01:06\) Task 68414 \| 02:26:49 \| Updating instance elasticsearch\_data: elasticsearch\_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece \(0\) \(canary\) \(00:02:10\) Task 68414 \| 02:28:59 \| Updating instance elasticsearch\_data: elasticsearch\_data/fa38698e-913c-4296-aac8-c0b56c84a71e \(1\) \(00:01:54\) Task 68414 \| 02:30:53 \| Updating instance collector: collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 \(0\) \(canary\) Task 68414 \| 02:30:53 \| Updating instance visualization: visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a \(0\) \(canary\) Task 68414 \| 02:30:53 \| Updating instance router: router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 \(0\) \(canary\) Task 68414 \| 02:30:53 \| Updating instance parser: parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 \(0\) \(canary\) Task 68414 \| 02:31:45 \| Updating instance router: router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 \(0\) \(canary\) \(00:00:52\) Task 68414 \| 02:31:55 \| Updating instance visualization: visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a \(0\) \(canary\) \(00:01:02\) Task 68414 \| 02:32:10 \| Updating instance parser: parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 \(0\) \(canary\) \(00:01:17\) Task 68414 \| 02:32:10 \| Updating instance parser: parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa \(1\) \(00:01:06\) Task 68414 \| 02:33:54 \| Updating instance collector: collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 \(0\) \(canary\) \(00:03:01\)

Task 68414 Started Wed Nov 28 02:06:49 UTC 2018 Task 68414 Finished Wed Nov 28 02:33:54 UTC 2018 Task 68414 Duration 00:27:05 Task 68414 done

Succeeded

```text
- 배포된 Logging 서비스를 확인한다.
```

$ bosh -e micro-bosh -d paasta-logging-service vms Using environment '10.30.40.111' as client 'admin'

Task 68432. Done

Deployment 'paasta-logging-service'

Instance Process State AZ IPs VM CID VM Type Active  
collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 running z5 10.30.107.131 vm-e73085ec-e336-4c54-a842-37989dc4fe1d default true  
elasticsearch\_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece running z5 10.30.107.133 vm-5b1fed2f-774f-47cf-9a14-edc015e790f1 medium true  
elasticsearch\_data/fa38698e-913c-4296-aac8-c0b56c84a71e running z5 10.30.107.134 vm-36a47dab-8d09-4daa-bb8c-0394f4d83fd7 medium true  
elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 running z5 10.30.107.135 vm-46152b8f-d660-413c-9396-8b4068a4a454 default true  
maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc running z5 10.30.107.136 vm-780e1595-9aa9-445c-b056-27ff4e844017 minimal true  
parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa running z5 10.30.107.138 vm-44210be7-0dab-46db-8cb6-d71a2c29d3c8 default true  
parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 running z5 10.30.107.137 vm-1eb78459-3050-4ab2-8f49-78f0ddb795b0 default true  
queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf running z5 10.30.107.139 vm-f11ec996-5c1e-46a0-972a-8b1415267df0 default true  
router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 running z5 10.30.107.140 vm-32ebc53c-6bef-48d7-854e-4b09a4dd9d01 minimal true  
115.68.47.181  
visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a running z5 10.30.107.143 vm-75fdb6a6-e77f-4adb-8336-ec77254c82fa default true

```text
## <div id="3"/>3.  Logging 서비스 관리

Logging 서비스 배포가 완료 되면, PaaS-TA 포탈에서 서비스를 사용하기 위해 Logging 서비스 UAA Client 등록 및 Logging 서비스 활성화 코드 등록을 해 주어야 한다.


### <div id="3.1"/>  3.1.    Logging 서비스 UAA Client 등록
-    Logging 서비스 접근이 가능한 IP를 확인한다.
```

$ bosh -e micro-bosh -d paasta-logging-service vms Using environment '10.30.40.111' as client 'admin'

Task 68432. Done

Deployment 'paasta-logging-service'

Instance Process State AZ IPs VM CID VM Type Active  
collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8 running z5 10.30.107.131 vm-e73085ec-e336-4c54-a842-37989dc4fe1d default true  
elasticsearch\_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece running z5 10.30.107.133 vm-5b1fed2f-774f-47cf-9a14-edc015e790f1 medium true  
elasticsearch\_data/fa38698e-913c-4296-aac8-c0b56c84a71e running z5 10.30.107.134 vm-36a47dab-8d09-4daa-bb8c-0394f4d83fd7 medium true  
elasticsearch\_master/4698c36b-413d-4370-b671-44ee075a0cf0 running z5 10.30.107.135 vm-46152b8f-d660-413c-9396-8b4068a4a454 default true  
maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc running z5 10.30.107.136 vm-780e1595-9aa9-445c-b056-27ff4e844017 minimal true  
parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa running z5 10.30.107.138 vm-44210be7-0dab-46db-8cb6-d71a2c29d3c8 default true  
parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2 running z5 10.30.107.137 vm-1eb78459-3050-4ab2-8f49-78f0ddb795b0 default true  
queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf running z5 10.30.107.139 vm-f11ec996-5c1e-46a0-972a-8b1415267df0 default true  
router/c64e9519-713c-4f24-9b04-4bbf2d0ac457 running z5 10.30.107.140 vm-32ebc53c-6bef-48d7-854e-4b09a4dd9d01 minimal true  
115.68.47.181  
visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a running z5 10.30.107.143 vm-75fdb6a6-e77f-4adb-8336-ec77254c82fa default true

```text
-    uaac server의 endpoint를 설정한다.
```

## endpoint 설정

$ uaac target [https://uaa](https://uaa). --skip-ssl-validation

## target 확인

$ uaac target Target: [https://uaa](https://uaa). Context: uaa\_admin, from client uaa\_admin

```text
-    uaac 로그인을 한다.
```

$ uaac token client get  -s  Successfully fetched token via client credentials grant. Target: [https://uaa](https://uaa). Context: admin, from client admin

```text
-    Logging 서비스 계정을 생성 한다.  
$ uaac client add <CF_UAA_CLIENT_ID> -s <CF_UAA_CLIENT_SECRET> --redirect_uri <Logging 서비스 URI> --scope <퍼미션 범위> --authorized_grant_types <권한 타입> --authorities=<권한 퍼미션> --autoapprove=<자동승인권한>  

  -    <CF_UAA_CLIENT_ID> : uaac 클라이언트 id  
  -    <CF_UAA_CLIENT_SECRET> : uaac 클라이언트 secret  
  -    <Logging 서비스 URI> : 성공적으로 리다이렉션 할 Logging 서비스 접근 URL (router public IP)  
  -    <퍼미션 범위> : 클라이언트가 사용자를 대신하여 얻을 수있는 허용 범위 목록  
  -    <권한 타입> : 서비스가 제공하는 API를 사용할 수 있는 권한 목록  
  -    <권한 퍼미션> : 클라이언트에 부여 된 권한 목록  
  -    <자동승인권한> : 사용자 승인이 필요하지 않은 권한 목록
```

## Logging 서비스 계정 생성

$ uaac client add laasclient -s clientsecret --redirect\_uri " [http://115.68.47.181](http://115.68.47.181)"  --scope "cloud\_controller\_service\_permissions.read , openid , cloud\_controller.read , cloud\_controller.write , cloud\_controller.admin"  --authorized\_grant\_types "authorization\_code , client\_credentials , refresh\_token"  --authorities="uaa.resource"  --autoapprove="openid , cloud\_controller\_service\_permissions.read"

## Logging 서비스 계정 생성 확인

$ uaac clients laasclient scope: cloud\_controller.read cloud\_controller.write cloud\_controller\_service\_permissions.read openid cloud\_controller.admin resource\_ids: none authorized\_grant\_types: refresh\_token client\_credentials authorization\_code redirect\_uri: [http://115.68.47.181](http://115.68.47.181) autoapprove: cloud\_controller\_service\_permissions.read openid authorities: uaa.resource name: laasclient lastmodified: 1542894096080

\`\`\`

### 3.2. Logging 서비스 활성화 코드 등록

* PaaS-TA 운영자 포탈에 접속한다. ![](../../.gitbook/assets/image002%20%282%29.png)
* 운영관리의 코드관리 메뉴로 이동하여 다음과 같이 코드를 등록한다.

> ※ Group Table  
> 코드 ID : LAAS  
> 코드 이름 : Logging Service  
> ![](../../.gitbook/assets/image003%20%282%29.png)
>
> ※ Detail Table  
> Key : laas\_base\_url  
> Value : http:///app/laas  
> 요약 : Logging Service Base URL  
> 사용 : Y  
> ![](../../.gitbook/assets/image004%20%282%29.png)

![](../../.gitbook/assets/image005%20%282%29.png)

