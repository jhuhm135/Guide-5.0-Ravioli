# Table of Contents

1. [문서 개요](paas-ta_cubrid_service_install_guide_v1.0.md#1)
   * [1.1. 목적](paas-ta_cubrid_service_install_guide_v1.0.md#2)
   * [1.2. 범위](paas-ta_cubrid_service_install_guide_v1.0.md#3)
   * [1.3. 시스템 구성도](paas-ta_cubrid_service_install_guide_v1.0.md#4)
   * [1.4. 참고자료](paas-ta_cubrid_service_install_guide_v1.0.md#5)
2. [Cubrid 서비스팩 설치](paas-ta_cubrid_service_install_guide_v1.0.md#6)
   * [2.1. 설치전 준비사항](paas-ta_cubrid_service_install_guide_v1.0.md#7)
   * [2.2. Cubrid 서비스 릴리즈 업로드](paas-ta_cubrid_service_install_guide_v1.0.md#8)
   * [2.3. Cubrid 서비스 Deployment 파일 수정 및 배포](paas-ta_cubrid_service_install_guide_v1.0.md#9)
   * [2.4. Cubrid 서비스 브로커 등록](paas-ta_cubrid_service_install_guide_v1.0.md#10)
3. [Cubrid 연동 Sample App 설명](paas-ta_cubrid_service_install_guide_v1.0.md#11)
   * [3.1. Sample App 구조](paas-ta_cubrid_service_install_guide_v1.0.md#12)
   * [3.2. PaaS-TA에서 서비스 신청](paas-ta_cubrid_service_install_guide_v1.0.md#13)
   * [3.3. Sample App에 서비스 바인드 신청 및 App 확인](paas-ta_cubrid_service_install_guide_v1.0.md#14)
4. [Cubrid Client 툴 접속](paas-ta_cubrid_service_install_guide_v1.0.md#15)
   * [4.1. Putty 다운로드 및 터널링](paas-ta_cubrid_service_install_guide_v1.0.md#16)
   * [4.2. Cubrid Manager 설치 및 연결](paas-ta_cubrid_service_install_guide_v1.0.md#17)

## 1. 문서 개요

### 1.1. 목적

본 문서\(Cubrid 서비스팩 설치 가이드\)는 전자정부표준프레임워크 기반의 PaaS-TA에서 제공되는 서비스팩인 Cubrid 서비스팩을 Bosh를 이용하여 설치 하는 방법과 PaaS-TA의 SaaS 형태로 제공하는 Application 에서 Cubrid 서비스를 사용하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위

설치 범위는 Cubrid 서비스팩을 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도

본 문서의 설치된 시스템 구성도입니다. Cubrid Server, Cubrid 서비스 브로커로 최소사항을 구성하였다.  
![](../../.gitbook/assets/1-3-0-0.png)

* 설치할때 cloud config에서 사용하는 VM\_Tpye명과 스펙 

| VM\_Type | 스펙 |
| :--- | :--- |
| minimal | 1vCPU / 1GB RAM / 8GB Disk |
| default | 1vCPU / 2GB RAM / 10GB Disk |

* 각 Instance의 Resource Pool과 스펙

| 구분 | Resource Pool | 스펙 |
| :--- | :--- | :--- |
| cubrid | default | 1vCPU / 1GB RAM / 8GB Disk |
| cubrid\_broker | minimal | 1vCPU / 2GB RAM / 10GB Disk |

### 1.4. 참고자료

[http://bosh.io/docs](http://bosh.io/docs)  
[http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)

## 2. Cubrid 서비스팩 설치

### 2.1. 설치전 준비사항

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스팩 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0, PaaS-TA 포털이 설치되어 있어야 한다.

* PaaS-TA에서 제공하는 압축된 릴리즈 파일들을 다운받는다. \(PaaSTA-Deployment.zip, PaaSTA-Sample-Apps.zip, PaaSTA-Services.zip\)
* 설치 파일 다운로드 위치

  > Download : [https://paas-ta.kr/download/package](https://paas-ta.kr/download/package)
  >
  > \`\`\`
  >
  > ## Deployment 다운로드 파일 위치 경로
  >
  > ~/workspace/paasta-5.0/deployment/service-deployment/paasta-cubrid-service-2.0

## 릴리즈 다운로드 파일 위치 경로

~/workspace/paasta-5.0/release/service

```text
<br>

### <div id='8'>  2.2. Cubrid 서비스 릴리즈 업로드

- Release Root 디렉토리로 이동하여 업로드 되어 있는 릴리즈 목록을 확인한다.

- **사용 예시**
```

$ bosh -e micro-bosh releases Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

Name Version Commit Hash  
binary-buildpack 1.0.21 _d714741  
bpm 0.9.0_ c9b7136  
caas-release 1.0 _empty+  
capi 1.62.0_ 22a608c  
cf-networking 2.8.0 _479f4a66  
cf-smoke-tests 40.0.5_ d6aaf1f  
cf-syslog-drain 7.0 _71b995a  
cflinuxfs2 1.227.0_ 60128e1  
consul 195 _67cdbcd  
diego 2.13.0_ b5644d9  
dotnet-core-buildpack 2.1.3 _46a41cd  
garden-runc 1.15.1_ 75107e7+  
go-buildpack 1.8.25 _40c60a0  
haproxy 8.8.0_ 9292573  
java-buildpack 4.13 _c2749d3  
loggregator 103.0_ 05da4e3d  
loggregator-agent 2.0 _2382c90  
nats 24_ 30e7a82  
nodejs-buildpack 1.6.28 _4cfdb7b  
paas-ta-portal-release 2.0_ non-git  
paasta-delivery-pipeline-release 1.0 _b3ee8f48+  
paasta-pinpoint 2.0_ 2dbb8bf3+  
php-buildpack 4.3.57 _efc48f3  
postgres 29_ 5de4d63d+  
python-buildpack 1.6.18 _bcc4f26  
routing 0.179.0_ 18155a5  
ruby-buildpack 1.7.21 _9d69600  
silk 2.9.0_ eebed55  
staticfile-buildpack 1.4.29 _8a82e63  
statsd-injector 1.3.0_ 39e5179  
uaa 60.2\* ebb5895

\(\*\) Currently deployed \(+\) Uncommitted changes

31 releases

Succeeded

```text
-    Cubrid 서비스 릴리즈가 업로드 되어 있지 않은 것을 확인

-    Cubrid 서비스 릴리즈를 업로드한다.

- **사용 예시**
```

$ bosh -e micro-bosh upload-release paasta-cubrid.tgz Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\) Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

**\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\# 100.00% 144.14 MiB/s 2s**

Task 4460

Task 4460 \| 04:31:41 \| Extracting release: Extracting release \(00:00:04\) Task 4460 \| 04:31:45 \| Verifying manifest: Verifying manifest \(00:00:00\) Task 4460 \| 04:31:45 \| Resolving package dependencies: Resolving package dependencies \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: gra-log-purger/f02fa5774ab54dbb1b1c3702d03cb929b85d60e6 \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: cf-mysql-broker/250c6466bdaff96677e501ed5219d92ce4e61bd8 \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: mysqlclient/ce95f8ac566f76b650992987d5282ee473356e43 \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: acceptance-tests/1cb3ce7e20f5a8395b43fc6f0e3f2e92b0dc27bd \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: galera/d15a1d2d15e5e7417278d4aa1b908566022b9623 \(00:00:01\) Task 4460 \| 04:31:46 \| Creating new packages: galera-healthcheck/3da4dedbcd7d9f404a19e7720e226fd472002266 \(00:00:00\) Task 4460 \| 04:31:46 \| Creating new packages: quota-enforcer/e2c4c9e7d7bbbe4bfdc0866962461b00e654cca3 \(00:00:00\) Task 4460 \| 04:31:46 \| Creating new packages: python/4e255efa754d91b825476b57e111345f200944e1 \(00:00:01\) Task 4460 \| 04:31:47 \| Creating new packages: ruby/ff79c965224b4160c1526bd704b3b21e4ad7c362 \(00:00:00\) Task 4460 \| 04:31:47 \| Creating new packages: route-registrar/f3fdfb8c940e7227a96c06e413ae6827aba8eeda \(00:00:00\) Task 4460 \| 04:31:47 \| Creating new packages: check/d6811f25e9d56428a9b942631c27c9b24f5064dc \(00:00:01\) Task 4460 \| 04:31:48 \| Creating new packages: cli/24305e50a638ece2cace4ef4803746c0c9fe4bb0 \(00:00:00\) Task 4460 \| 04:31:48 \| Creating new packages: mariadb/43aa3547bc5a01dd51f1501e6b93c215dd7255e9 \(00:00:01\) Task 4460 \| 04:31:49 \| Creating new packages: openjdk-1.8.0\_45/57e0ee876ea9d90f5470e3784ae1171bccee850a \(00:00:02\) Task 4460 \| 04:31:51 \| Creating new packages: mariadb\_ctrl/7658290da98e2cad209456f174d3b9fa143c87fc \(00:00:01\) Task 4460 \| 04:31:52 \| Creating new packages: scons/11e7ad3b28b43a96de3df7aa41afddde582fcc38 \(00:00:00\) Task 4460 \| 04:31:52 \| Creating new packages: syslog\_aggregator/078da6dcb999c1e6f5398a6eb739182ccb4aba25 \(00:00:00\) Task 4460 \| 04:31:52 \| Creating new packages: xtrabackup/2e701e7a9e4241b28052d984733de36aae152275 \(00:00:01\) Task 4460 \| 04:31:53 \| Creating new packages: boost/3eb8bdb1abb7eff5b63c4c5bdb41c0a778925c31 \(00:00:01\) Task 4460 \| 04:31:54 \| Creating new packages: common/ba480a46c4b2aa9484fb24ed01a8649453573e6f \(00:00:00\) Task 4460 \| 04:31:54 \| Creating new packages: switchboard/fad565dadbb37470771801952001c7071e55a364 \(00:00:01\) Task 4460 \| 04:31:55 \| Creating new packages: op-mysql-java-broker/3bf47851b2c0d3bea63a0c58452df58c14a15482 \(00:00:01\) Task 4460 \| 04:31:56 \| Creating new packages: golang/f57ddbc8d55d7a0f08775bf76bb6a27dc98c7ea7 \(00:00:01\) Task 4460 \| 04:31:57 \| Creating new jobs: cf-mysql-broker/9828ead15eabdc33b2c27fe275b463735edb115d \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: acceptance-tests/48c00c36ec5210cbdd3b125ae6a72cfdf6eaf4e2 \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: broker-deregistrar/b5f6f776d46eb1ac561ab1e8f58d8ddedb97f86e \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: proxy/7907d8759aa11dfcbbe79220dc945c96b5562ac1 \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: mysql/078561f02f2516212ed59c48e1dd45360f93871c \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: op-mysql-java-broker/6e47c9ea6fbe0867d4a476af5abf157830c03024 \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: broker-registrar/e1f5e30b87e70e916ea74ea8eb63a7b6ff6ff643 \(00:00:00\) Task 4460 \| 04:31:57 \| Release has been created: paasta-mysql/2.0 \(00:00:00\)

Task 4460 Started Fri Aug 31 04:31:41 UTC 2018 Task 4460 Finished Fri Aug 31 04:31:57 UTC 2018 Task 4460 Duration 00:00:16 Task 4460 done

Succeeded

```text
- 업로드 된 Cubrid 릴리즈를 확인한다. 

- **사용 예시**
```

$ bosh -e micro-bosh releases Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

Name Version Commit Hash  
binary-buildpack 1.0.21 _d714741  
bpm 0.9.0_ c9b7136  
caas-release 1.0 _empty+  
capi 1.62.0_ 22a608c  
cf-networking 2.8.0 _479f4a66  
cf-smoke-tests 40.0.5_ d6aaf1f  
cf-syslog-drain 7.0 _71b995a  
cflinuxfs2 1.227.0_ 60128e1  
consul 195 _67cdbcd  
diego 2.13.0_ b5644d9  
dotnet-core-buildpack 2.1.3 _46a41cd  
garden-runc 1.15.1_ 75107e7+  
go-buildpack 1.8.25 _40c60a0  
haproxy 8.8.0_ 9292573  
java-buildpack 4.13 _c2749d3  
loggregator 103.0_ 05da4e3d  
loggregator-agent 2.0 _2382c90  
nats 24_ 30e7a82  
nodejs-buildpack 1.6.28 _4cfdb7b  
paas-ta-portal-release 2.0_ non-git  
paasta-delivery-pipeline-release 1.0 _b3ee8f48+  
paasta-cubrid 2.0 85e3f01e+  
paasta-pinpoint 2.0_ 2dbb8bf3+  
php-buildpack 4.3.57 _efc48f3  
postgres 29_ 5de4d63d+  
python-buildpack 1.6.18 _bcc4f26  
routing 0.179.0_ 18155a5  
ruby-buildpack 1.7.21 _9d69600  
silk 2.9.0_ eebed55  
staticfile-buildpack 1.4.29 _8a82e63  
statsd-injector 1.3.0_ 39e5179  
uaa 60.2\* ebb5895

\(\*\) Currently deployed \(+\) Uncommitted changes

32 releases

Succeeded

```text
-    Deploy시 사용할 Stemcell을 확인한다.

- **사용 예시**
```

$ bosh -e micro-bosh stemcells Name Version OS CPI CID  
bosh-openstack-kvm-ubuntu-xenial-go\_agent 315.64\* ubuntu-xenial - 240752b1-b1f9-43ed-8e96-7f3e4f269d71

\(\*\) Currently deployed

1 stemcells

Succeeded

```text
>Stemcell 목록이 존재 하지 않을 경우 BOSH 설치 가이드 문서를 참고 하여 Stemcell을 업로드를 해야 한다. 

### <div id='9'> 2.3. Cubrid 서비스 Deployment 파일 및 deploy-cubrid-bosh2.0.sh 수정 및 배포

BOSH Deployment manifest 는 components 요소 및 배포의 속성을 정의한 YAML 파일이다.
Deployment manifest 에는 sotfware를 설치 하기 위해서 어떤 Stemcell (OS, BOSH agent) 을 사용할것이며 Release (Software packages, Config templates, Scripts) 이름과 버전, VMs 용량, Jobs params 등을 정의가 되어 있다.

deployment 파일에서 사용하는 network, vm_type 등은 cloud config 를 활용하고 해당 가이드는 Bosh2.0 가이드를 참고한다.

-    cloud config 내용 조회

- **사용 예시**
```

$ bosh -e micro-bosh cloud-config Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

azs:

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z1

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z2

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z3

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z4

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z5

* cloud\_properties:

    datacenters:

  * clusters:
    * BD-HA:

      resource\_pool: CF\_BOSH2\_Pool

      name: BD-HA

      name: z6

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

* disk\_size: 2048

  name: 2GB

* disk\_size: 4096

  name: 4GB

* disk\_size: 5120

  name: 5GB

* disk\_size: 8192

  name: 8GB

* disk\_size: 10240

  name: 10GB

* disk\_size: 20480

  name: 20GB

* disk\_size: 30720

  name: 30GB

* disk\_size: 51200

  name: 50GB

* disk\_size: 102400

  name: 100GB

* disk\_size: 1048576

  name: 1TB

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

* name: public

  subnets:

  * azs:
    * z1
    * z2
    * z3
    * z4
    * z5
    * z6

      cloud\_properties:

      name: External

      dns:

    * 8.8.8.8

      gateway: 115.68.46.177

      range: 115.68.46.176/28

      reserved:

    * 115.68.46.176 - 115.68.46.188

      static:

    * 115.68.46.189 - 115.68.46.190

      type: manual

* name: service\_private

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

    * 10.30.0.0 - 10.30.106.255

      static:

    * 10.30.107.1 - 10.30.107.255

* name: service\_public

  subnets:

  * azs:
    * z1
    * z2
    * z3
    * z4
    * z5
    * z6

      cloud\_properties:

      name: External

      dns:

    * 8.8.8.8

      gateway: 115.68.47.161

      range: 115.68.47.160/24

      reserved:

    * 115.68.47.161 - 115.68.47.174

      static:

    * 115.68.47.175 - 115.68.47.185

      type: manual

* name: portal\_service\_public

  subnets:

  * azs:
    * z1
    * z2
    * z3
    * z4
    * z5
    * z6

      cloud\_properties:

      name: External

      dns:

    * 8.8.8.8

      gateway: 115.68.46.209

      range: 115.68.46.208/28

      reserved:

    * 115.68.46.216 - 115.68.46.222

      static:

    * 115.68.46.214

      type: manual

      vm\_extensions:

* cloud\_properties:

    ports:

  * host: 3306

    name: mysql-proxy-lb

* name: cf-router-network-properties
* name: cf-tcp-router-network-properties
* name: diego-ssh-proxy-network-properties
* name: cf-haproxy-network-properties
* cloud\_properties:

    disk: 51200

  name: small-50GB

* cloud\_properties:

    disk: 102400

  name: small-highmem-100GB

  vm\_types:

* cloud\_properties:

    cpu: 1

    disk: 8192

    ram: 1024

  name: minimal

* cloud\_properties:

    cpu: 1

    disk: 10240

    ram: 2048

  name: default

* cloud\_properties:

    cpu: 1

    disk: 30720

    ram: 4096

  name: small

* cloud\_properties:

    cpu: 2

    disk: 20480

    ram: 4096

  name: medium

* cloud\_properties:

    cpu: 2

    disk: 20480

    ram: 8192

  name: medium-memory-8GB

* cloud\_properties:

    cpu: 4

    disk: 20480

    ram: 8192

  name: large

* cloud\_properties:

    cpu: 8

    disk: 20480

    ram: 16384

  name: xlarge

* cloud\_properties:

    cpu: 2

    disk: 51200

    ram: 4096

  name: small-50GB

* cloud\_properties:

    cpu: 2

    disk: 51200

    ram: 4096

  name: small-50GB-ephemeral-disk

* cloud\_properties:

    cpu: 4

    disk: 102400

    ram: 8192

  name: small-100GB-ephemeral-disk

* cloud\_properties:

    cpu: 4

    disk: 102400

    ram: 8192

  name: small-highmem-100GB-ephemeral-disk

* cloud\_properties:

    cpu: 8

    disk: 20480

    ram: 16384

  name: small-highmem-16GB

* cloud\_properties:

    cpu: 1

    disk: 4096

    ram: 2048

  name: caas\_small

* cloud\_properties:

    cpu: 1

    disk: 4096

    ram: 1024

  name: caas\_small\_api

* cloud\_properties:

    cpu: 1

    disk: 4096

    ram: 4096

  name: caas\_medium

* cloud\_properties:

    cpu: 2

    disk: 8192

    ram: 4096

  name: service\_medium

* cloud\_properties:

    cpu: 2

    disk: 10240

    ram: 2048

  name: service\_medium\_2G

Succeeded

```text
-    Deployment 파일을 서버 환경에 맞게 수정한다.

```yml
# paasta-cubrid-service 설정 파일 내용
---
name: "((deployment_name))"                            # 서비스 배포이름(필수)

releases:
- name: "((releases_name))"                            # 서비스 릴리즈 이름(필수)
  version: "2.0"                                       # 서비스 릴리즈 버전(필수):latest 시 업로드된 서비스 릴리즈 최신버전

stemcells:
- alias: default
  os: ((stemcell_os))
  version: "((stemcell_version))"

update:
  canaries: 1                                          # canary 인스턴스 수(필수)
  canary_watch_time: 30000-180000                      # canary 인스턴스가 수행하기 위한 대기 시간(필수)
  max_in_flight: 6                                     # non-canary 인스턴스가 병렬로 update 하는 최대 개수(필수)
  update_watch_time: 30000-180000                      # non-canary 인스턴스가 수행하기 위한 대기 시간(필수)

instance_groups:
- name: cubrid                                         # 작업 이름(필수): Cubrid 서버
  azs:
  - z5
  instances: 1                                         # job 인스턴스 수(필수)
  vm_type: ((vm_type_default))                         # cloud config 에 정의한 vm_type
  stemcell: default
  networks:
  - name: ((default_network_name))                     # cloud config 에 정의한 network 이름
  jobs:
  - name: cubrid
    release: "((releases_name))"

- name: cubrid_broker                                  # 작업 이름(필수): Cubrid 서비스 브로커
  azs:
  - z5
  instances: 1                                         # job 인스턴스 수(필수)
  vm_type: ((vm_type_small))                           # cloud config 에 정의한 vm_type
  stemcell: default
  networks:
  - name: ((default_network_name))                     # cloud config 에 정의한 network 이름
  jobs:
  - name: cubrid_broker                                # job template 이름(필수)
    release: "((releases_name))"
properties:
  cubrid:   # Cubrid 설정 정보
    max_clients: 200
  cubrid_broker:                                       # Cubrid Servcice Broker 설정 정보
    cubrid_db_port: 30000                              # Cubrid Port
    cubrid_db_name: cubrid_broker                      # Cubrid service 관리를 위한 데이터베이스 이름
    cubrid_db_user: dba                                # 브로커 관리용 데이터베이스 접근 사용자이름
    cubrid_db_passwd: paasta                           # 브로커 관리용 데이터베이스 접근 사용자 비밀번호
    cubrid_ssh_port: 22                                # Cubrid가 설치된 서버 SSH 접속 포트
    cubrid_ssh_user: vcap                              # Cubrid가 설치된 서버 SSH 접속 사용자 이름
    cubrid_ssh_key: ((ssh_key))
```

* deploy-cubrid-{IaaS}.sh 파일을 서버 환경에 맞게 수정한다.

```bash
#!/bin/bash

bosh -d paasta-cubrid-service deploy paasta_cubrid.yml \
   -v deployment_name=paasta-cubrid-service \
   -v releases_name=paasta-cubrid \
   -v default_network_name=default \
   -v stemcell_os=ubuntu-xenial \
   -v stemcell_version="315.64" \
   -v vm_type_small=minimal \
   -v vm_type_default=medium \
   -l pem.yml  (또는)  -v ssh_key='""'   ##### AWS/OPENSTACK의 경우 -l pem.yml , GCP/VSPHERE/AZURE의 경우 -v ssh_key='""'
```

* pem.yml 수정한다. \(AWS/OPENSTACK의 경우 설정 필요\)

  > pem.yml :: bosh create-env 시에 private\_key로 설정했던 key의 value

```bash
ssh_key: |
        -----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA24kRZy6XCkduExlJs0NEDEXGMWPzpMctDQOTaIwE6KmYJkUU
skDyydyJ1uuvBMmckkJPJtbKp7H2R2EuUEYbRqMuNvti1DtKDIomu+7J5i5BezkU
... ((생략)) ...
UT4edXsdO2hPmmTJY63LywiRHbQ5oHdPFqoUbOJFsw5/msJMo5Lfpc7F5KhLlkbR
G/GIqOk+vwkB8x//oOelECn4gWxeue0G11YNG5MrcWuVU7ijLyT6
        -----END RSA PRIVATE KEY-----
```

* Cubrid 서비스팩을 배포한다.
* **사용 예시**

  \`\`\` $ sh deploy-cubrid-{IaaS}.sh Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

Using deployment 'paasta-cubrid-service'

* azs:
* * cloud\_properties:
* datacenters:
* * clusters:
* * BD-HA:
* resource\_pool: PaaS\_TA\_50\_Pools
* name: BD-HA
* name: z1

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
* * z7
* cloud\_properties:
* name: Internal
* dns:
* * 8.8.8.8
* gateway: 10.30.20.23
* range: 10.30.0.0/16
* reserved:
* * 10.30.0.0 - 10.30.51.255
* * 10.30.100.0 - 10.30.255.255
* static:
* * 10.30.52.0 - 10.30.55.255
* type: manual

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
* * name: paasta-cubrid
* version: '2.0'
* update:
* canaries: 1
* canary\_watch\_time: 30000-180000
* max\_in\_flight: 6
* update\_watch\_time: 30000-180000

... \(\(생략\)\) ...

* instance\_groups:
* * azs:
* * z5
* instances: 1
* jobs:
* * name: cubrid
* release: paasta-cubrid
* name: cubrid
* networks:
* * name: service\_private
* stemcell: default
* vm\_type: medium
* * azs:
* * z5
* instances: 1
* jobs:
* * name: cubrid\_broker
* release: paasta-cubrid
* name: cubrid\_broker
* networks:
* * name: service\_private
* stemcell: default
* vm\_type: minimal
* name: paasta-cubrid-service
* properties:
* cubrid:
* max\_clients: ""
* cubrid\_broker:
* cubrid\_db\_name: ""
* cubrid\_db\_passwd: ""
* cubrid\_db\_port: ""
* cubrid\_db\_user: ""
* cubrid\_ssh\_key: ""
* cubrid\_ssh\_port: ""
* cubrid\_ssh\_user: ""

Continue? \[yN\]: y

Task 524

Task 524 \| 08:13:12 \| Preparing deployment: Preparing deployment \(00:00:06\) Task 524 \| 08:13:18 \| Preparing deployment: Rendering templates \(00:00:00\) Task 524 \| 08:13:18 \| Preparing package compilation: Finding packages to compile \(00:00:01\) Task 524 \| 08:13:19 \| Compiling packages: java7/f6046e69cd156721da1a5ad08efb9d9d278583abd1319c202e1167585ef4014b Task 524 \| 08:13:19 \| Compiling packages: cubrid/e8dda2260d3a8fbf77b474e4989381b241c2ca8aa1bfc1f3e5d465712151ffea Task 524 \| 08:15:29 \| Compiling packages: java7/f6046e69cd156721da1a5ad08efb9d9d278583abd1319c202e1167585ef4014b \(00:02:10\) Task 524 \| 08:15:29 \| Compiling packages: cubrid\_broker/0a4685029ea46058603890003f69dd1bd5f829564e2687a2ab8103ed4937594c \(00:00:37\) Task 524 \| 08:16:25 \| Compiling packages: cubrid/e8dda2260d3a8fbf77b474e4989381b241c2ca8aa1bfc1f3e5d465712151ffea \(00:03:06\) Task 524 \| 08:16:59 \| Creating missing vms: cubrid/e60ec9e8-4834-4655-a1f0-f8e2468d0ff5 \(0\) Task 524 \| 08:16:59 \| Creating missing vms: cubrid\_broker/4e887b24-c621-492f-bd92-f061f7c0ce80 \(0\) \(00:01:23\) Task 524 \| 08:18:23 \| Creating missing vms: cubrid/e60ec9e8-4834-4655-a1f0-f8e2468d0ff5 \(0\) \(00:01:24\) Task 524 \| 08:18:24 \| Updating instance cubrid: cubrid/e60ec9e8-4834-4655-a1f0-f8e2468d0ff5 \(0\) \(canary\) \(00:01:05\) Task 524 \| 08:19:29 \| Updating instance cubrid\_broker: cubrid\_broker/4e887b24-c621-492f-bd92-f061f7c0ce80 \(0\) \(canary\) \(00:00:52\)

Task 524 Started Mon Nov 18 08:13:12 UTC 2019 Task 524 Finished Mon Nov 18 08:20:21 UTC 2019 Task 524 Duration 00:07:09 Task 524 done

Succeeded

```text
-    배포된 Cubrid 서비스팩을 확인한다.

- **사용 예시**
```

$ bosh -e micro-bosh -d paasta-cubrid-service vms Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

Task 6424. Done

Deployment 'paasta-cubrid-service'

Instance Process State AZ IPs VM CID VM Type Active  
cubrid/e60ec9e8-4834-4655-a1f0-f8e2468d0ff5 running z5 10.30.101.0 vm-1a5805f4-8e7b-4e3e-bf17-270976d2b566 medium true  
cubrid\_broker/4e887b24-c621-492f-bd92-f061f7c0ce80 running z5 10.30.101.1 vm-77133451-0d7a-4be0-9d24-ae094ca12ea9 minimal true

2 vms

Succeeded

```text
### <div id='10'> 2.4. Cubrid 서비스 브로커 등록
Cubrid 서비스팩 배포가 완료 되었으면 Application에서 서비스 팩을 사용하기 위해서 먼저 Cubrid 서비스 브로커를 등록해 주어야 한다.  
서비스 브로커 등록시 PaaS-TA에서 서비스브로커를 등록할 수 있는 사용자로 로그인이 되어있어야 한다.

##### 서비스 브로커 목록을 확인한다.

>`cf service-brokers`
```

$ cf service-brokers Getting service brokers as admin...

name url No service brokers found

```text
##### Cubrid 서비스 브로커를 등록한다.

>`$ cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]`
> - [SERVICE_BROKER] : 서비스 브로커 명  
> - [USERNAME] / [PASSWORD] : 서비스 브로커에 접근할 수 있는 사용자 ID / PASSWORD  
> - [SERVICE_BROKER_URL] : 서비스 브로커 접근 URL

>`cf create-service-broker cubrid-service-broker admin cloudfoundry http://10.30.101.1:8080`
```

$ cf create-service-broker cubrid-service-broker admin cloudfoundry [http://10.30.101.1:8080](http://10.30.101.1:8080) Creating service broker cubrid-service-broker as admin... OK

```text
##### 등록된 Cubrid 서비스 브로커를 확인한다.

>`$ cf service-brokers`
```

$ cf service-brokers Getting service brokers as admin...

name url cubrid-service-broker [http://10.30.101.1:8080](http://10.30.101.1:8080)

```text
- 접근 가능한 서비스 목록을 확인한다.

>`$ cf service-access`
```

$ cf service-access Getting service access as admin... broker: cubrid-service-broker service plan access orgs CubridDB utf8 none CubridDB euckr none

```text
##### 서비스 브로커 생성시 디폴트로 접근을 허용하지 않는다.

##### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. (전체 조직)

>`$ cf enable-service-access CubridDB `<br>
>`$ cf service-access `
```

$ cf enable-service-access CubridDB Enabling access to all plans of service CubridDB for all orgs as admin... OK

$ cf service-access Getting service access as admin... broker: cubrid-service-broker service plan access orgs CubridDB utf8 all CubridDB euckr all

```text
#  <div id='11'> 3. Cubrid연동 Sample App 설명
본 Sample Web App은 PaaS-TA에 배포되며 Cubrid의 서비스를 Provision과 Bind를 한 상태에서 사용이 가능하다.

### <div id='12'> 3.1. Sample App 구조
Sample Web App은 PaaS-TA에 App으로 배포가 된다. App을 배포하여 구동시 Bind 된 Cubrid 서비스 연결정보로 접속하여 초기 데이터를 생성하게 된다. 배포 완료 후 정상적으로 App 이 구동되면 브라우져나 curl로 해당 App에 접속 하여 Cubrid 환경정보(서비스 연결 정보)와 초기 적재된 데이터를 보여준다.

Sample Web App 구조는 다음과 같다.
<table>
  <tr>
    <td>이름</td>
    <td>설명</td>
  </tr>
  <tr>
    <td>src</td>
    <td>Sample 소스 디렉토리</td>
  </tr>
  <tr>
    <td>manifest.yml</td>
    <td>PaaS-TA에 app 배포시 필요한 설정을 저장하는 파일</td>
  </tr>
  <tr>
    <td>pom.xml</td>
    <td>메이븐 project 설정 파일</td>
  </tr>
  <tr>
    <td>target</td>
    <td>메이븐 빌드시 생성되는 디렉토리(war 파일, classes 폴더 등)</td>
  </tr>
</table>

##### PaaS-TA-Sample-Apps을 다운로드 받고 Service 폴더안에 있는 Cubrid Sample Web App인 hello-spring-cubrid를 복사한다.

>`$ ls -all `

>![3-1-0-0]

<br>

### <div id='13'> 3.2. PaaS-TA에서 서비스 신청
Sample Web App에서 Cubrid 서비스를 사용하기 위해서는 서비스 신청(Provision)을 해야 한다.  
(참고: 서비스 신청시 PaaS-TA에서 서비스를신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.)

##### 먼저 PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다.

>`$ cf marketplace `
```

$ cf marketplace Getting services from marketplace in org demo / space dev as demo... OK

service plans description CubridDB utf8, euckr A simple cubrid implementation

TIP: Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.

```text
##### Marketplace에서 원하는 서비스가 있으면 서비스 신청(Provision)을 한다.

>`$ cf create-service {서비스명} {서비스플랜} {내서비스명}  `

**서비스명** CubridDB로 Marketplace에서 보여지는 서비스 명칭이다.  
**서비스플랜** 서비스에 대한 정책으로 plans에 있는 정보 중 하나를 선택한다. Cubrid 서비스는 100mb, 1gb를 지원한다.  
**내서비스명**내 서비스에서 보여지는 명칭이다. 이 명칭을 기준으로 환경설정정보를 가져온다.  

>`$ cf create-service CubridDB utf8 cubrid-service-instance `
```

$ cf create-service CubridDB utf8 cubrid-service-instance Creating service instance cubrid-service-instance in org demo / space dev as demo... OK

```text
##### 생성된 Cubrid 서비스 인스턴스를 확인한다.

>`$ cf services`
```

$ cf services Getting services in org demo / space dev as demo... OK

name service plan bound apps last operation cubrid-service-instance CubridDB utf8 create succeeded

```text
### <div id='14'> 3.3. Sample App에 서비스 바인드 신청 및 App 확인
서비스 신청이 완료되었으면 Sample Web App 에서는 생성된 서비스 인스턴스를 Bind 하여 App에서 Cubrid 서비스를 이용한다.  
*참고: 서비스 Bind 신청시 PaaS-TA에서 서비스 Bind신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.



##### Sample Web App 디렉토리로 이동하여 manifest 파일을 확인한다.
다운로드 :: http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download
```

$ wget -O sample.zip [http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download](http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download) $ unzip sample.zip -d sample $ cd sample/Service/hello-spring-cubrid

```text
>`$ vi manifest.yml` <br>
```

applications:

* name: hello-spring-cubrid \# 배포할 App 이름 memory: 1024M \# 배포시 메모리 사이즈 instances: 1 \# 배포 인스턴스 수 path: target/hello-spring-cubrid-1.0.0-BUILD-SNAPSHOT.war \#배포하는 App 파일 PATH

  \`\`\`

* 참고: ./build/libs/hello-spring-cubrid.war 파일이 존재 하지 않을 경우 gradle 빌드를 수행 하면 파일이 생성된다.

**--no-start 옵션으로 App을 배포한다.**

**--no-start: App 배포시 구동은 하지 않는다.**

> `$ cf push --no-start`

```text
$ cf push --no-start
Using manifest file D:\sample-app\hello-spring-cubrid\manifest.yml

Creating app hello-spring-cubrid in org demo / space dev as demo...
OK

Creating route hello-spring-cubrid.115.68.47.178.xip.io...
OK

Binding hello-spring-cubrid.115.68.47.178.xip.io to hello-spring-cubrid...
OK

Uploading hello-spring-cubrid...
Uploading app files from: C:\Users\demo\AppData\Local\Temp\unzipped-app845663023
Uploading 7.2M, 59 files
Done uploading
OK
```

**배포된 Sample App을 확인하고 로그를 수행한다.**

> `$ cf apps`
>
> \`\`\`  
> $ cf apps Getting apps in org demo / space dev as demo... OK

name requested state instances memory disk urls hello-spring-cubrid stopped 0/1 1G 1G hello-spring-cubrid.115.68.47.178.xip.io sampleapp started 1/1 1G 1G sampleapp.115.68.47.178.xip.io

```text
>`$ cf logs {배포된 App명}` <br>
>`$ cf logs hello-spring-cubrid`
```

$ cf logs hello-spring-cubrid Retrieving logs for app hello-spring-cubrid in org demo / space dev as demo...

```text
###### Sample Web App에서 생성한 서비스 인스턴스 바인드 신청을 한다. 

>`$ cf bind-service hello-spring-cubrid cubrid-service-instance`
```

$ cf bind-service hello-spring-cubrid cubrid-service-instance Binding service cubrid-service-instance to app hello-spring-cubrid in org demo / space dev as demo... OK TIP: Use 'cf restage hello-spring-cubrid' to ensure your env variable changes take effect

```text
##### 바인드가 적용되기 위해서 App을 재기동한다.
>`$ cf restart hello-spring-cubrid`
```

$ cf restart hello-spring-cubrid

Starting app hello-spring-cubrid in org demo / space dev as demo... Downloading binary\_buildpack... Downloading java\_buildpack... Downloading go\_buildpack... Downloading nodejs\_buildpack... Downloading php\_buildpack... Downloaded php\_buildpack \(301.5K\) Downloading nginx\_buildpack... Downloading r\_buildpack... Downloaded java\_buildpack \(224.7K\) Downloaded go\_buildpack \(5M\) Downloading python\_buildpack... Downloaded nodejs\_buildpack \(5.1M\) Downloading ruby\_buildpack... Downloaded r\_buildpack \(4.8M\) Downloading dotnet\_core\_buildpack... Downloaded binary\_buildpack \(8.2M\) Downloaded nginx\_buildpack \(8.2M\) Downloading staticfile\_buildpack... Downloaded python\_buildpack \(5M\) Downloaded ruby\_buildpack \(5M\) Downloaded dotnet\_core\_buildpack \(5.3M\) Downloaded staticfile\_buildpack \(5.6M\) Cell 215698eb-793e-4271-80e0-2fd7962f93b6 creating container for instance abd243fa-552d-49d4-81f8-685dc26af007 Cell 215698eb-793e-4271-80e0-2fd7962f93b6 successfully created container for instance abd243fa-552d-49d4-81f8-685dc26af007 Downloading app package... Downloaded app package \(7.6M\) -----&gt; Java Buildpack v4.19.1 \| [https://github.com/cloudfoundry/java-buildpack.git\#3f4eee2](https://github.com/cloudfoundry/java-buildpack.git#3f4eee2) -----&gt; Downloading Jvmkill Agent 1.16.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/jvmkill/bionic/x86\_64/jvmkill-1.16.0-RELEASE.so](https://java-buildpack.cloudfoundry.org/jvmkill/bionic/x86_64/jvmkill-1.16.0-RELEASE.so) \(0.3s\) -----&gt; Downloading Open Jdk JRE 1.8.0\_212 from [https://java-buildpack.cloudfoundry.org/openjdk/bionic/x86\_64/openjdk-jre-1.8.0\_212-bionic.tar.gz](https://java-buildpack.cloudfoundry.org/openjdk/bionic/x86_64/openjdk-jre-1.8.0_212-bionic.tar.gz) \(1.2s\) Expanding Open Jdk JRE to .java-buildpack/open\_jdk\_jre \(1.3s\) JVM DNS caching disabled in lieu of BOSH DNS caching -----&gt; Downloading Open JDK Like Memory Calculator 3.13.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/memory-calculator/bionic/x86\_64/memory-calculator-3.13.0-RELEASE.tar.gz](https://java-buildpack.cloudfoundry.org/memory-calculator/bionic/x86_64/memory-calculator-3.13.0-RELEASE.tar.gz) \(0.1s\) Loaded Classes: 10196, Threads: 250 -----&gt; Downloading Client Certificate Mapper 1.8.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/client-certificate-mapper/client-certificate-mapper-1.8.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/client-certificate-mapper/client-certificate-mapper-1.8.0-RELEASE.jar) \(0.0s\) -----&gt; Downloading Container Security Provider 1.16.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/container-security-provider/container-security-provider-1.16.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/container-security-provider/container-security-provider-1.16.0-RELEASE.jar) \(5.1s\) -----&gt; Downloading Spring Auto Reconfiguration 2.7.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/auto-reconfiguration/auto-reconfiguration-2.7.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/auto-reconfiguration/auto-reconfiguration-2.7.0-RELEASE.jar) \(0.4s\) -----&gt; Downloading Tomcat Instance 9.0.19 from [https://java-buildpack.cloudfoundry.org/tomcat/tomcat-9.0.19.tar.gz](https://java-buildpack.cloudfoundry.org/tomcat/tomcat-9.0.19.tar.gz) \(0.3s\) Expanding Tomcat Instance to .java-buildpack/tomcat \(0.2s\) -----&gt; Downloading Tomcat Access Logging Support 3.3.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/tomcat-access-logging-support/tomcat-access-logging-support-3.3.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/tomcat-access-logging-support/tomcat-access-logging-support-3.3.0-RELEASE.jar) \(0.1s\) -----&gt; Downloading Tomcat Lifecycle Support 3.3.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/tomcat-lifecycle-support/tomcat-lifecycle-support-3.3.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/tomcat-lifecycle-support/tomcat-lifecycle-support-3.3.0-RELEASE.jar) \(5.0s\) -----&gt; Downloading Tomcat Logging Support 3.3.0\_RELEASE from [https://java-buildpack.cloudfoundry.org/tomcat-logging-support/tomcat-logging-support-3.3.0-RELEASE.jar](https://java-buildpack.cloudfoundry.org/tomcat-logging-support/tomcat-logging-support-3.3.0-RELEASE.jar) \(0.1s\) Exit status 0 Uploading droplet, build artifacts cache... Uploading droplet... Uploading build artifacts cache... Uploaded build artifacts cache \(53.7M\) Uploaded droplet \(60.1M\) Uploading complete Cell 215698eb-793e-4271-80e0-2fd7962f93b6 stopping instance abd243fa-552d-49d4-81f8-685dc26af007 Cell 215698eb-793e-4271-80e0-2fd7962f93b6 destroying container for instance abd243fa-552d-49d4-81f8-685dc26af007 Cell 215698eb-793e-4271-80e0-2fd7962f93b6 successfully destroyed container for instance abd243fa-552d-49d4-81f8-685dc26af007

0 of 1 instances running, 1 starting 0 of 1 instances running, 1 starting 0 of 1 instances running, 1 starting 0 of 1 instances running, 1 starting 1 of 1 instances running

App started

OK

App hello-spring-cubrid was started using this command `JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.16.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc) -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS -Daccess.logging.enabled=false -Dhttp.port=$PORT" && CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=12719 -poolType=metaspace -stackThreads=250 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 JAVA_OPTS=$JAVA_OPTS JAVA_HOME=$PWD/.java-buildpack/open_jdk_jre exec $PWD/.java-buildpack/tomcat/bin/catalina.sh run`

Showing health and status for app hello-spring-cubrid in org demo / space dev as demo... OK

requested state: started instances: 1/1 usage: 1G x 1 instances urls: hello-spring-cubrid.115.68.47.178.xip.io last uploaded: Mon Nov 18 08:47:44 UTC 2019 stack: cflinuxfs3 buildpack: client-certificate-mapper=1.8.0\_RELEASE container-security-provider=1.16.0\_RELEASE java-buildpack=v4.19.1-[https://github.com/cloudfoundry/java-buildpack.git\#3f4eee2](https://github.com/cloudfoundry/java-buildpack.git#3f4eee2) java-opts java-security jvmkill-agent=1.16.0\_RELEASE open-jdk-like-jre=1.8.0\_2...

```text
 state     since                    cpu    memory       disk           details
```

## 0   running   2019-11-18 06:10:42 PM   0.0%   125M of 1G   128.8M of 1G

```text
- (참고) 바인드 후 App구동시 Cubrid 서비스 접속 에러로 App 구동이 안될 경우 보안 그룹을 추가한다.
```

**rule.json 화일을 만들고 아래와 같이 내용을 넣는다.**

$ vi rule.json \[ { "protocol": "tcp", "destination": "10.30.101.0", "ports": "30000" } \]

**보안 그룹을 생성한 후, 모든 App에 Cubrid 서비스를 사용할수 있도록 생성한 보안 그룹을 적용한다.**

$ cf create-security-group CubridDB rule.json $ cf bind-running-security-group CubridDB

**App을 리부팅 한다.**

$ cf restart hello-spring-cubrid

```text
##### App이 정상적으로 Cubrid 서비스를 사용하는지 확인한다.

##### curl 로 확인
>`$ curl hello-spring-cubrid.115.68.47.178.xip.io`
```

$ curl hello-spring-cubrid.115.68.47.178.xip.io

Hello Spring Cubrid

## Hello Spring Cubrid!

#### Database Info:

 DataSource: jdbc:cubrid:10.30.101.0::bd545a9031ffed9f:d795c4b014d3c5ce:3e114ab842f46dff:  


#### Current States:

 State \[id=1, stateCode=MA, name=Massachusetts\]  
 State \[id=2, stateCode=NH, name=New Hampshire\]  
 State \[id=3, stateCode=ME, name=Maine\]  
 State \[id=4, stateCode=VT, name=Vermont\]  


\`\`\`

**브라우져에서 확인**

> ![](../../.gitbook/assets/3-3-8-1.png)

## 4. Cubrid Client 툴 접속

Application에 바인딩된 Cubrid 서비스 연결정보는 Private IP로 구성되어 있기 때문에 Cubrid Client 툴에서 직접 연결할수 없다. 따라서 Cubrid Client 툴에서 SSH 터널, Proxy 터널 등을 제공하는 툴을 사용해서 연결하여야 한다. 본 가이드는 무료 SSH 및 텔넷 접속 툴인 Putty를 이용하여 SSH 터널을 통해 연결 하는 방법을 제공하며 Cubrid Client 툴로써는 Cubrid에서 제공하는 Cubrid Manager로 가이드한다. Cubrid Manager 에서 접속하기 위해서 먼저 SSH 터널링 할수 있는 VM 인스턴스를 생성해야한다. 이 인스턴스는 SSH로 접속이 가능해야 하고 접속 후 Open PaaS 에 설치한 서비스팩에 Private IP 와 해당 포트로 접근이 가능하도록 시큐리티 그룹을 구성해야 한다. 이 부분은 vSphere관리자 및 OpenPaaS 운영자에게 문의하여 구성한다.

### 4.1.  Putty 다운로드 및 터널링

Putty 프로그램은 SSH 및 텔넷 접속을 할 수 있는 무료 소프트웨어이다.

* Putty를 다운로드 하기 위해 아래 URL로 이동하여 파일을 다운로드 한다. 별도의 설치과정없이 사용할 수 있다.

  [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)  

  ![](../../.gitbook/assets/4-1-0-0.png)

**다운받은 putty.exe.파일을 더블클릭하여 실행한다.**

> ![](../../.gitbook/assets/4-1-1-0.png)

**Session 탭의 Host name과 Port란에. OpenPaaS 운영 관리자에게 제공받은 SSH 터널링 가능한 서버 정보를 입력한다.**

> ![](../../.gitbook/assets/4-1-2-0.png)

**Connection-&gt;SSH-&gt;Tunnels 탭에서 Source port\(내 로컬에서 접근할 포트\), Destination\(터널링으로 연결할 서버정보\)를 입력하고 Local, Auto를 선택 후 Add를 클릭한다.**

![](../../.gitbook/assets/4-1-3-0.png)  
![](../../.gitbook/assets/4-1-3-1.png)  
서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env  명령어로 이용하여 확인한다.  
예\) $ cf env hello-spring-cubrid

> ![](../../.gitbook/assets/4-1-4-0.png)

**Session 탭에서 Saved Sessions에 저장할 이름을 입력하고 Save를 눌러 저장한 후 Open버튼을 누른다.**

> ![](../../.gitbook/assets/4-1-5-0.png)

**서버 접속정보를 입력하여 접속하여 터널링을 완료한다.**

만약 ssh 인증이 Password방식이 아닌 Key인증 방식일 경우, Connection-&gt;SSH-&gt;인증탭의 '인증 개인키 파일'에 key 파일을 등록하여 인증한다.  
Key파일의 확장자가 .pem이라면 putty설치시 같이 설치된 puttygen을 사용하여 ppk파일로 변환한뒤 사용한다.

> ![](../../.gitbook/assets/4-1-6-0.png)

### 4.2.  Cubrid Manager 설치 & 연결

Cubrid Manager 프로그램은 Cubrid에서 제공하는 무료로 사용할 수 있는 소프트웨어이다.

* Cubrid Manager를 다운로드 하기 위해 아래 URL로 이동하여 설치파일을 다운로드 한다.  

  [http://ftp.cubrid.org/CUBRID\_Tools/CUBRID\_Manager/](http://ftp.cubrid.org/CUBRID_Tools/CUBRID_Manager/)  

  ![](../../.gitbook/assets/4-2-0-0.png)

**다운받은 파일을 더블클릭하여 실행한다.**

> ![](../../.gitbook/assets/4-2-1-0.png)

**한국어를 선택하고 OK를 누른다.**

> ![](../../.gitbook/assets/4-2-2-0.png)

**다음을 눌러 계속 진행한다.**

> ![](../../.gitbook/assets/4-2-3-0.png)

**동의함을 눌러 계속 진행한다.**

> ![](../../.gitbook/assets/4-2-4-0.png)

**바로가기 옵션을 선택 후 다음을 눌러 계속 진행한다.**

> ![](../../.gitbook/assets/4-2-5-0.png)

**설치 경로를 입력하고 설치를 눌러 설치를 시작한다.**

> ![](../../.gitbook/assets/4-2-6-0.png)

**설치가 완료되면 다음을 눌러 계속 진행한다.**

> ![](../../.gitbook/assets/4-2-7-0.png)

**마침을 눌러 설치를 완료한다.**

> ![](../../.gitbook/assets/4-2-8-0.png)

**설치된 Cubrid Manager를 실행하면 처음 나오는 화면이다. Workspace를 선택 후 OK를 눌러 실행한다. 만약 이 창을 다시보기를 원치않는다면 '기본적으로 이것을 사용하고 다시 물어 보지 않기' 옵션을 선택한다.**

> ![](../../.gitbook/assets/4-2-9-0.png)

**관리 모드, 질의 모드 둘중 목적에 맞게 선택 후 확인을 눌러 실행한다. 여기서는 질의 모드로 실행한다.**

> ![](../../.gitbook/assets/4-2-10-0.png)

**연결정보를 입력하기 위해서 연결 정보 등록을 누른다.**

> ![](../../.gitbook/assets/4-2-11-0.png)

**Server에 접속하기 위한 Connection 정보를 입력한다.**

> ![](../../.gitbook/assets/4-2-12-0.png)

**서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env  명령어로 이용하여 확인한다.**

> `cf env hello-spring-cubrid`
>
> ![](../../.gitbook/assets/4-2-13-0.png)

**연결 테스트 버튼을 클릭하여 접속 테스트를 한다.**

> ![](../../.gitbook/assets/4-2-14-0.png)

**정보가 정상적으로 입력되었다면 '연결이 성공하였습니다.'라는 메시지가 나온다.**

확인 버튼을 눌러 창을 닫는다.

> ![](../../.gitbook/assets/4-2-15-0.png)

**연결 버튼을 클릭하여 접속한다**

> ![](../../.gitbook/assets/4-2-16-0.png)

**접속이 완료되면 좌측에 스키마 정보가 나타난다.**

> ![](../../.gitbook/assets/4-2-17-0.png)

**질의 편집기 버튼을 클릭하면 오른쪽 창에 query를 입력할 수 있는 창이 열린다.**

> ![](../../.gitbook/assets/4-2-18-0.png)

**우측 화면에 쿼리 항목에 Query문을 작성한 후 실행 버튼\(삼각형\)을 클릭한다. 쿼리문에 이상이 없다면 정상적으로 결과를 얻을 수 있을 것이다.**

> ![](../../.gitbook/assets/4-2-19-0.png)

