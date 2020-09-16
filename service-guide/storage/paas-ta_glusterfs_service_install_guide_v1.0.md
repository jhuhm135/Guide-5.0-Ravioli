# PAAS-TA\_GLUSTERFS\_SERVICE\_INSTALL\_GUIDE\_V1.0

### Table of Contents

1. [문서 개요](paas-ta_glusterfs_service_install_guide_v1.0.md#1)
   * [1.1. 목적](paas-ta_glusterfs_service_install_guide_v1.0.md#2)
   * [1.2. 범위](paas-ta_glusterfs_service_install_guide_v1.0.md#3)
   * [1.3. 시스템 구성도](paas-ta_glusterfs_service_install_guide_v1.0.md#4)
   * [1.4. 참고자료](paas-ta_glusterfs_service_install_guide_v1.0.md#5)
2. [GlusterFS 서비스팩 설치](paas-ta_glusterfs_service_install_guide_v1.0.md#6)
   * [2.1. 설치전 준비사항](paas-ta_glusterfs_service_install_guide_v1.0.md#7)
   * [2.2. GlusterFS 서비스 릴리즈 업로드](paas-ta_glusterfs_service_install_guide_v1.0.md#8)
   * [2.3. GlusterFS 서비스 Deployment 파일 수정 및 배포](paas-ta_glusterfs_service_install_guide_v1.0.md#9)
   * [2.4. GlusterFS 서비스 브로커 등록](paas-ta_glusterfs_service_install_guide_v1.0.md#10)
3. [GlusterFS 연동 Sample App 설명](paas-ta_glusterfs_service_install_guide_v1.0.md#11)
   * [3.1. Sample App 구조](paas-ta_glusterfs_service_install_guide_v1.0.md#12)
   * [3.2. PaaS-TA에서 서비스 신청](paas-ta_glusterfs_service_install_guide_v1.0.md#13)

### 1. 문서 개요

#### 1.1. 목적

본 문서\(GlusterFS 서비스팩 설치 가이드\)는 전자정부 표준 프레임워크 기반의 PaaS-TA에서 제공되는 서비스팩인 GlusterFS 서비스팩을 Bosh를 이용하여 설치 하는 방법과 PaaS-TA의 SaaS 형태로 제공하는 Application 에서GlusterFS 서비스를 사용하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

#### 1.2. 범위

설치 범위는 GlusterFS 서비스팩을 검증하기 위한 기본 설치를 기준으로 작성하였다.

#### 1.3. 시스템 구성도

본 문서의 설치된 시스템 구성도이다. Mysql Server, GlusterFS 서비스 브로커로 최소사항을 구성하였고 서비스 백엔드는 외부에 구성되어 있다. ![](../../.gitbook/assets/glusterfs_image_01.png)

* 설치할때 cloud config에서 사용하는 VM\_Tpye명과 스펙 

| VM\_Type | 스펙 |
| :--- | :--- |
| minimal | 1vCPU / 1GB RAM / 8GB Disk |

* 각 Instance의 Resource Pool과 스펙

| 구분 | Resource Pool | 스펙 |
| :--- | :--- | :--- |
| paasta-glusterfs-broker | minimal | 1vCPU / 1GB RAM / 8GB Disk |
| mysql | minimal | 1vCPU / 1GB RAM / 8GB Disk |

 \#\#\# 1.4. 참고자료 \[\*\*http://bosh.io/docs\*\*\]\(http://bosh.io/docs\)  
 \[\*\*http://docs.cloudfoundry.org/\*\*\]\(http://docs.cloudfoundry.org/\) \#\#2. GlusterFS 서비스팩 설치

#### 2.1. 설치전 준비사항

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스팩 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0, PaaS-TA 포털이 설치되어 있어야 한다.

* PaaS-TA에서 제공하는 압축된 릴리즈 파일들을 다운받는다. \(PaaSTA-Deployment.zip, PaaSTA-Sample-Apps.zip, PaaSTA-Services.zip\)
* 설치 파일 다운로드 위치

  > Download : [https://paas-ta.kr/download/package](https://paas-ta.kr/download/package)
  >
  > \`\`\`
  >
  > ## Deployment 다운로드 파일 위치 경로
  >
  > ~/workspace/paasta-5.0/deployment/service-deployment/paasta-glusterfs-service

## 릴리즈 다운로드 파일 위치 경로

~/workspace/paasta-5.0/release/service

```text
### <div id='8'>2.2. GlusterFS 서비스 릴리즈 업로드</div>

-    업로드 되어 있는 릴리즈 목록을 확인한다.

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
-    GlusterFS 서비스 릴리즈가 업로드 되어 있지 않은 것을 확인

-    GlusterFS 서비스 릴리즈 파일을 업로드한다.

- **사용 예시**
```

$ bosh -e micro-bosh upload-release paasta-glusterfs-2.0.tgz Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

**\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\# 100.00% 144.14 MiB/s 2s**

Task 4460

Task 4460 \| 04:31:41 \| Extracting release: Extracting release \(00:00:04\) Task 4460 \| 04:31:45 \| Verifying manifest: Verifying manifest \(00:00:00\) Task 4460 \| 04:31:45 \| Resolving package dependencies: Resolving package dependencies \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: gra-log-purger/f02fa5774ab54dbb1b1c3702d03cb929b85d60e6 \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: mysqlclient/ce95f8ac566f76b650992987d5282ee473356e43 \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: acceptance-tests/1cb3ce7e20f5a8395b43fc6f0e3f2e92b0dc27bd \(00:00:00\) Task 4460 \| 04:31:45 \| Creating new packages: galera/d15a1d2d15e5e7417278d4aa1b908566022b9623 \(00:00:01\) Task 4460 \| 04:31:46 \| Creating new packages: galera-healthcheck/3da4dedbcd7d9f404a19e7720e226fd472002266 \(00:00:00\) Task 4460 \| 04:31:46 \| Creating new packages: quota-enforcer/e2c4c9e7d7bbbe4bfdc0866962461b00e654cca3 \(00:00:00\) Task 4460 \| 04:31:46 \| Creating new packages: python/4e255efa754d91b825476b57e111345f200944e1 \(00:00:01\) Task 4460 \| 04:31:47 \| Creating new packages: ruby/ff79c965224b4160c1526bd704b3b21e4ad7c362 \(00:00:00\) Task 4460 \| 04:31:47 \| Creating new packages: route-registrar/f3fdfb8c940e7227a96c06e413ae6827aba8eeda \(00:00:00\) Task 4460 \| 04:31:47 \| Creating new packages: check/d6811f25e9d56428a9b942631c27c9b24f5064dc \(00:00:01\) Task 4460 \| 04:31:48 \| Creating new packages: cli/24305e50a638ece2cace4ef4803746c0c9fe4bb0 \(00:00:00\) Task 4460 \| 04:31:48 \| Creating new packages: mariadb/43aa3547bc5a01dd51f1501e6b93c215dd7255e9 \(00:00:01\) Task 4460 \| 04:31:49 \| Creating new packages: openjdk-1.8.0\_45/57e0ee876ea9d90f5470e3784ae1171bccee850a \(00:00:02\) Task 4460 \| 04:31:51 \| Creating new packages: mariadb\_ctrl/7658290da98e2cad209456f174d3b9fa143c87fc \(00:00:01\) Task 4460 \| 04:31:52 \| Creating new packages: scons/11e7ad3b28b43a96de3df7aa41afddde582fcc38 \(00:00:00\) Task 4460 \| 04:31:52 \| Creating new packages: syslog\_aggregator/078da6dcb999c1e6f5398a6eb739182ccb4aba25 \(00:00:00\) Task 4460 \| 04:31:52 \| Creating new packages: xtrabackup/2e701e7a9e4241b28052d984733de36aae152275 \(00:00:01\) Task 4460 \| 04:31:53 \| Creating new packages: boost/3eb8bdb1abb7eff5b63c4c5bdb41c0a778925c31 \(00:00:01\) Task 4460 \| 04:31:54 \| Creating new packages: common/ba480a46c4b2aa9484fb24ed01a8649453573e6f \(00:00:00\) Task 4460 \| 04:31:54 \| Creating new packages: switchboard/fad565dadbb37470771801952001c7071e55a364 \(00:00:01\) Task 4460 \| 04:31:56 \| Creating new packages: golang/f57ddbc8d55d7a0f08775bf76bb6a27dc98c7ea7 \(00:00:01\) Task 4460 \| 04:31:57 \| Creating new jobs: acceptance-tests/48c00c36ec5210cbdd3b125ae6a72cfdf6eaf4e2 \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: broker-deregistrar/b5f6f776d46eb1ac561ab1e8f58d8ddedb97f86e \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: proxy/7907d8759aa11dfcbbe79220dc945c96b5562ac1 \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: mysql/078561f02f2516212ed59c48e1dd45360f93871c \(00:00:00\) Task 4460 \| 04:31:57 \| Creating new jobs: broker-registrar/e1f5e30b87e70e916ea74ea8eb63a7b6ff6ff643 \(00:00:00\) Task 4460 \| 04:31:57 \| Release has been created: paasta-mysql/2.0 \(00:00:00\)

Task 4460 Started Fri Aug 31 04:31:41 UTC 2018 Task 4460 Finished Fri Aug 31 04:31:57 UTC 2018 Task 4460 Duration 00:00:16 Task 4460 done

Succeeded

```text
-    업로드 된 GlusterFS 릴리즈를 확인한다.

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
paasta-glusterfs 2.0 85e3f01e+  
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
bosh-openstack-kvm-ubuntu-xenial-go\_agent 315.64\* ubuntu-xenial - fb08e389-2350-4091-9b29-41743495e62c

\(\*\) Currently deployed

1 stemcells

Succeeded

```text
### <div id='9'>2.3. glusterfs 서비스 Deployment 파일 수정 및 배포</div>
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
# paasta-swift-object-service 설정 파일 내용
name: paasta-glusterfs-service                       # 서비스 배포이름(필수)

releases:
- name: paasta-glusterfs                             # 서비스 릴리즈 이름(필수)
  version: "2.0"                                     # 서비스 릴리즈 버전(필수):latest 시 업로드된 서비스 릴리즈 최신버전

update:
  canaries: 1                                        # canary 인스턴스 수(필수)
  canary_watch_time: 30000-600000                    # canary 인스턴스가 수행하기 위한 대기 시간(필수)
  update_watch_time: 30000-600000                    # non-canary 인스턴스가 수행하기 위한 대기 시간(필수)
  max_in_flight: 1                                   # non-canary 인스턴스가 병렬로 update 하는 최대 개수(필수)

stemcells:
- alias: default
  os: ubuntu-xenial
  version: latest

instance_groups:
- instances: 1                                       # job 인스턴스 수(필수)
  name: mysql                                        # 작업 이름(필수): MySQL 서버
  azs:
  - z3
  stemcell: default
  networks:                                          # 네트워크 구성정보
  - name: default                                    # Networks block에서 선언한 network 이름(필수)
    static_ips: 10.0.81.196                          # 사용할 IP addresses 정의(필수): MySQL 서버 IP
#  persistent_disk: 1024                             # 영구적 디스크 사이즈 정의(옵션): 1G, 상황에 맞게 수정
  vm_type: medium
  jobs: 
  - name: mysql                                      # job template 이름(필수)
    release: paasta-glusterfs
    properties:                                      # job에 대한 속성을 지정(필수)
      admin_username: root                           # MySQL 어드민 계정
      admin_password: admin                          # MySQL 어드민 패스워드

- instances: 1
  name: paasta-glusterfs-broker
  azs:
  - z3
  networks:
  - name: default
  stemcell: default
  vm_type: medium
  jobs: 
  - name: op-glusterfs-java-broker
    release: paasta-glusterfs 
    properties:
      jdbc_ip: 10.0.81.196               # Mysql IP(필수)
      jdbc_pwd: admin                    # Mysql password(필수)
      jdbc_port: 3306                    # Mysql Port
      log_dir: paasta-glusterfs-broker   # Broker Log 저장 디렉토리 명
      log_file: paasta-glusterfs-broker  # Broker Log 저장 파일 명
      log_level: INFO                    # Broker Log 단계
      glusterfs_url: 52.201.48.51        # Glusterfs 서비스 주소
      glusterfs_tenantname: service      # Glusterfs 서비스 테넌트 이름
      glusterfs_username: swift          # Glusterfs 서비스 계정 아이디
      glusterfs_password: password       # Glusterfs 서비스 암호

- instances: 1
  azs:
  - z3
  lifecycle: errand  # bosh deploy시 vm에 생성되어 설치 되지 않고 bosh errand 로실행할때 설정, 주로 테스트 용도에 쓰임
  stemcell: default
  name: broker-registrar
  networks:
  - name: default
  vm_type: medium
  jobs: 
  - name: broker-registrar
    release: paasta-glusterfs
    properties:
      broker:
        host: 10.0.81.197            # Service Broker IP
        name: glusterfs-service      # Service Broker Name
        password: cloudfoundry       # Service Broker Auth Password
        username: admin              # Service Broker Auth Id
        protocol: http               # Service Broker Http Protocol
        port: 8080                   # Service Broker port
      cf:
        admin_password: admin                      # CF Paasword
        admin_username: admin                      # CF Id
        api_url: https://api.<DOMAIN>.xip.io       # CF Target Url
        skip_ssl_validation: true                  # CF SSL 설정

- instances: 1
  azs:
  - z3
  lifecycle: errand
  stemcell: default
  name: broker-deregistrar
  networks:
  - name: default
  vm_type: medium
  jobs: 
  - name: broker-deregistrar
    release: paasta-glusterfs
    properties:
      broker:
      name: glusterfs-service
      cf:
        admin_password: admin
        admin_username: admin
        api_url: https://<DOMAIN>.xip.io
        skip_ssl_validation: true
properties: {}
```

* deploy\_glusterfs.sh 파일을 서버 환경에 맞게 수정한다.

```bash
bosh -d paasta-glusterfs-service deploy paasta_glusterfs_2.0.yml
```

* GlusterFS 서비스팩을 배포한다.
* **사용 예시**

  \`\`\`  
  $ sh deploy\_glusterfs.sh Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\) Using environment '10.30.50.1' as client 'admin'

Using deployment 'paasta-glusterfs-service'

* azs:
* * cloud\_properties:
* datacenters:
* * clusters:
* * BD-HA:
* resource\_pool: CF\_BOSH2\_Pool
* name: BD-HA
* name: z1

... \(\(생략\)\) ...

* vm\_types:
* * cloud\_properties:
* cpu: 1
* disk: 8192
* ram: 1024
* name: minimal
* * cloud\_properties:
* cpu: 1
* disk: 10240
* ram: 2048
* name: default
* * cloud\_properties:
* cpu: 1
* disk: 20480
* ram: 4096
* name: small
* * cloud\_properties:
* cpu: 2
* disk: 20480
* ram: 4096
* name: medium
* * cloud\_properties:
* cpu: 2
* disk: 20480
* ram: 8192
* name: medium-memory-8GB
* * cloud\_properties:
* cpu: 4
* disk: 20480
* ram: 8192
* name: large
* * cloud\_properties:
* cpu: 8
* disk: 20480
* ram: 16384
* name: xlarge
* * cloud\_properties:
* cpu: 2
* disk: 51200
* ram: 4096
* name: small-50GB
* * cloud\_properties:
* cpu: 2
* disk: 51200
* ram: 4096
* name: small-50GB-ephemeral-disk
* * cloud\_properties:
* cpu: 4
* disk: 102400
* ram: 8192
* name: small-100GB-ephemeral-disk
* * cloud\_properties:
* cpu: 4
* disk: 102400
* ram: 8192
* name: small-highmem-100GB-ephemeral-disk
* * cloud\_properties:
* cpu: 8
* disk: 20480
* ram: 16384
* name: small-highmem-16GB
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 2048
* name: caas\_small
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 1024
* name: caas\_small\_api
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 4096
* name: caas\_medium
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 256
* name: service\_tiny
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 512
* name: service\_small
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 1024
* name: service\_medium
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 2048
* name: service\_medium\_1CPU\_2G
* * cloud\_properties:
* cpu: 2
* disk: 8192
* ram: 4096
* name: service\_medium\_4G
* * cloud\_properties:
* cpu: 2
* disk: 10240
* ram: 2048
* name: service\_medium\_2G
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 256
* name: portal\_tiny
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 512
* name: portal\_small
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 1024
* name: portal\_medium
* * cloud\_properties:
* cpu: 1
* disk: 4096
* ram: 2048
* name: portal\_large

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
* * name: paasta-glusterfs
* version: '2.0'
* update:
* canaries: 1
* canary\_watch\_time: 30000-600000
* max\_in\_flight: 1
* update\_watch\_time: 30000-600000

... \(\(생략\)\) ...

* instance\_groups:
* * azs:
* * z3
* instances: 1
* jobs:
* * name: mysql
* properties:
* admin\_password: ""
* admin\_username: ""
* release: paasta-glusterfs
* name: mysql
* networks:
* * name: default
* static\_ips: 10.30.52.10
* stemcell: default
* vm\_type: medium
* * azs:
* * z3
* instances: 1
* jobs:
* * name: op-glusterfs-java-broker
* properties:
* glusterfs\_password: ""
* glusterfs\_tenantname: ""
* glusterfs\_url: ""
* glusterfs\_username: ""
* jdbc\_ip: ""
* jdbc\_port: ""
* jdbc\_pwd: ""
* log\_dir: ""
* log\_file: ""
* log\_level: ""
* release: paasta-glusterfs
* name: paasta-glusterfs-broker
* networks:
* * name: default
* static\_ips: 10.30.52.11
* stemcell: default
* vm\_type: medium
* * azs:
* * z3
* instances: 1
* jobs:
* * name: broker-registrar
* properties:
* broker:
* host: ""
* name: ""
* password: ""
* port: ""
* protocol: ""
* username: ""
* cf:
* admin\_password: ""
* admin\_username: ""
* api\_url: ""
* skip\_ssl\_validation: ""
* release: paasta-glusterfs
* lifecycle: errand
* name: broker-registrar
* networks:
* * name: default
* stemcell: default
* vm\_type: medium
* * azs:
* * z3
* instances: 1
* jobs:
* * name: broker-deregistrar
* properties:
* broker: ""
* cf:
* admin\_password: ""
* admin\_username: ""
* api\_url: ""
* skip\_ssl\_validation: ""
* name: ""
* release: paasta-glusterfs
* lifecycle: errand
* name: broker-deregistrar
* networks:
* * name: default
* stemcell: default
* vm\_type: medium
* name: paasta-glusterfs-service
* properties: {}

Continue? \[yN\]: y

Task 1342

Task 1342 \| 12:31:10 \| Preparing deployment: Preparing deployment \(00:00:07\) Task 1342 \| 12:31:17 \| Preparing deployment: Rendering templates \(00:00:00\) Task 1342 \| 12:31:17 \| Preparing package compilation: Finding packages to compile \(00:00:00\) Task 1342 \| 12:31:17 \| Compiling packages: cli/24305e50a638ece2cace4ef4803746c0c9fe4bb0 Task 1342 \| 12:31:17 \| Compiling packages: mariadb/76d00089f1c7ee1122f6b584d26d21a14254e1f0 Task 1342 \| 12:31:17 \| Compiling packages: op-gluster-java-broker/e281dffd1a22142658f57509183afa9be6be2983 Task 1342 \| 12:31:17 \| Compiling packages: openjdk/566dfae383c61dff0c9e82bee373bb68bac3e10e Task 1342 \| 12:34:20 \| Compiling packages: cli/24305e50a638ece2cace4ef4803746c0c9fe4bb0 \(00:03:03\) Task 1342 \| 12:34:25 \| Compiling packages: openjdk/566dfae383c61dff0c9e82bee373bb68bac3e10e \(00:03:08\) Task 1342 \| 12:34:26 \| Compiling packages: op-gluster-java-broker/e281dffd1a22142658f57509183afa9be6be2983 \(00:03:09\) Task 1342 \| 13:05:22 \| Compiling packages: mariadb/76d00089f1c7ee1122f6b584d26d21a14254e1f0 \(00:34:05\) Task 1342 \| 13:06:21 \| Creating missing vms: mysql/8770bc70-8681-4079-8360-086219d6231b \(0\) Task 1342 \| 13:06:21 \| Creating missing vms: paasta-glusterfs-broker/229fb890-645b-4213-89a1-fc2116de3f54 \(0\) \(00:01:24\) Task 1342 \| 13:07:45 \| Creating missing vms: mysql/8770bc70-8681-4079-8360-086219d6231b \(0\) \(00:01:24\) Task 1342 \| 13:07:46 \| Updating instance mysql: mysql/8770bc70-8681-4079-8360-086219d6231b \(0\) \(canary\) \(00:01:00\) Task 1342 \| 13:08:46 \| Updating instance paasta-glusterfs-broker: paasta-glusterfs-broker/229fb890-645b-4213-89a1-fc2116de3f54 \(0\) \(canary\) \(00:00:53\)

Task 1342 Started Thu Nov 21 12:31:10 UTC 2019 Task 1342 Finished Thu Nov 21 13:09:39 UTC 2019 Task 1342 Duration 00:38:29 Task 1342 done

Succeeded

```text
-    배포된 GlusterFS 서비스팩을 확인한다.

- **사용 예시**
```

$ bosh -e micro-bosh -d paasta-glusterfs-service vms Using environment '10.30.40.111' as user 'admin' \(openid, bosh.admin\)

Task 1343. Done

Deployment 'paasta-glusterfs-service'

Instance Process State AZ IPs VM CID VM Type Active  
mysql/8770bc70-8681-4079-8360-086219d6231b running z3 10.30.52.10 vm-96697221-0ff9-4520-8a68-2314c62057a5 medium true  
paasta-glusterfs-broker/229fb890-645b-4213-89a1-fc2116de3f54 running z3 10.30.52.11 vm-ace55b8f-3ce0-4482-b03b-96fbc567592e medium true

2 vms

Succeeded

```text
### <div id='10'>2.4. GlusterFS 서비스 브로커 등록</div>  

GlusterFS 서비스팩 배포가 완료 되었으면 Application에서 서비스 팩을 사용하기 위해서 먼저 GlusterFS 서비스 브로커를 등록해 주어야 한다.
서비스 브로커 등록시에는 PaaS-TA에서 서비스 브로커를 등록할 수 있는 사용자로 로그인 하여야 한다

##### 서비스 브로커 목록을 확인한다.

>`$ cf service-brokers`
```

$ cf service-brokers Getting service brokers as admin...

name url No service brokers found

```text
##### GlusterFS 서비스 브로커를 등록한다.  
> $ cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]
> 
> [SERVICE_BROKER] : 서비스 브로커 명
> [USERNAME] / [PASSWORD] : 서비스 브로커에 접근할 수 있는 사용자 ID / PASSWORD
> [SERVICE_BROKER_URL] : 서비스 브로커 접근 URL
>`$ cf create-service-broker glusterfs-service admin cloudfoundry http://10.30.107.197:8080`
```

$ cf create-service-broker glusterfs-service admin cloudfoundry [http://10.30.107.197:8080](http://10.30.107.197:8080) Creating service broker glusterfs-service as admin... OK

```text
##### 등록된 GlusterFS 서비스 브로커를 확인한다.

>`$ cf service-brokers`
```

$ cf service-brokers Getting service brokers as admin...

name url glusterfs-service [http://10.30.107.197:8080](http://10.30.107.197:8080)

```text
##### 접근 가능한 서비스 목록을 확인한다.

>`$ cf service-access`
```

$ cf service-access Getting service access as admin... broker: glusterfs-service service plan access orgs glusterfs glusterfs-5Mb none glusterfs glusterfs-100Mb none glusterfs glusterfs-1000Mb none

```text
>서비스 브로커 등록시 최초에는 접근을 허용하지 않는다. 따라서 access는 none으로 설정된다.

##### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. (전체 조직)

>`$ cf enable-service-access glusterfs`  
>`$ cf service-access`
```

$ cf enable-service-access glusterfs Enabling access to all plans of service glusterfs for all orgs as admin... OK

$ cf service-access Getting service access as admin... broker: glusterfs-service service plan access orgs glusterfs glusterfs-5Mb all glusterfs glusterfs-100Mb all glusterfs glusterfs-1000Mb all

\`\`\`

### 3. GlusterFS 연동 Sample App 설명

본 Sample Web App은 PaaS-TA에 배포되며 GlusterFS의 서비스를 Provision과 Bind를 한 상태에서 사용이 가능하다.

#### 3.1. Sample App 구조

Sample Web App은 PaaS-TA에 App으로 배포가 된다. 배포 완료 후 정상적으로 App 이 구동되면 브라우저나 curl로 해당 App에 접속 하여 GlusterFS 환경정보\(서비스 연결 정보\)와파일 업로드하고 확인하는 기능을 제공한다.

Sample App 구조는 다음과 같다.

| 이름 | 설명 |
| :--- | :--- |
| src | Sample 소스디렉토리 |
| manifest | PaaS-TA에 app 배포시 필요한 설정을 저장하는 파일 |
| pom.xml | maven project 설정 파일 |
| target | maven build시 생성되는 디렉토리\(war 파일, classes 폴더 등\) |

**PaaSTA-Sample-Apps.zip 파일 압축을 풀고 Service폴더안에 있는 GlusterFSSample Web App인 hello-spring-glusterfs를 복사한다.**

> `$ ls -all`
>
> ![](../../.gitbook/assets/glusterfs_image_07.png)

#### 3.2. PaaS-TA에서 서비스 신청

Sample App에서 GlusterFS 서비스를 사용하기 위해서는 서비스 신청\(Provision\)을 해야 한다. \*참고: 서비스 신청시 PaaS-TA에서 서비스를 신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.

**먼저 PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다.**

> `$ cf marketplace`
>
> ![](../../.gitbook/assets/glusterfs_image_08.png)

**Marketplace에서 원하는 서비스가 있으면 서비스 신청\(Provision\)을 한다.**

> `$ cf create-service {서비스명} {서비스 플랜} {내 서비스명}`
>
> * **서비스명** : p-rabbitmq로 Marketplace에서 보여지는 서비스 명칭이다.
> * **서비스플랜** : 서비스에 대한 정책으로 plans에 있는 정보 중 하나를 선택한다. RabbitMQ 서비스는 standard plan만 지원한다.
> * **내 서비스명** : 내 서비스에서 보여지는 명칭이다. 이 명칭을 기준으로 환경 설정 정보를 가져온다.
>
> `$ cf create-service glusterfs glusterfs-1000Mb glusterfs-service-instance`
>
> ![](../../.gitbook/assets/glusterfs_image_09.png)

**생성된 GlusterFS 서비스 인스턴스를 확인한다.**

> `$ cf services`
>
> ![](../../.gitbook/assets/glusterfs_image_10.png)

**브라우에서 이미지 확인**

> ![](../../.gitbook/assets/glusterfs_image_17.jpeg)

