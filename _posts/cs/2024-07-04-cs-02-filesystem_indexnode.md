---
title: "[CS] Filesystem & Index Node"
description: "파일시스템과 인덱스 노드(Inode) 쉽게 이해하기"
author: aijinsol
date: 2024-07-04
categories:
  - ComputerScience
tags:
  - filesystem
  - inode
---

# 1. 파일시스템

파일 시스템은 데이터를 저장하고 관리하는 시스템입이다. <br>
우리가 컴퓨터나 스마트폰에서 사용하는 모든 파일은 파일 시스템에 의해 체계적으로 관리된다. <br>
파일 시스템은 다음과 같은 정보들을 관리한다.

- 파일의 이름
- 파일의 크기
- 파일의 위치
- 파일의 접근 권한

# 2. 디스크 용량 확인하기

디스크 용량을 확인할 때 사용하는 `df -h` 명령어는 파일 시스템의 상태를 보여준다. `-h` 옵션은 사람이 읽기 쉬운 형태로 출력하라는 의미이다.

```bash
df -h
Filesystem        Size    Used   Avail Capacity iused ifree %iused  Mounted on
/dev/disk3s1s1   926Gi    13Gi   197Gi     6%    404k  2.1G    0%   /
devfs            211Ki   211Ki     0Bi   100%     732     0  100%   /dev
/dev/disk3s6     926Gi    20Ki   197Gi     1%       0  2.1G    0%   /System/Volumes/VM
/dev/disk3s2     926Gi    11Gi   197Gi     6%    1.5k  2.1G    0%   /System/Volumes/Preboot
/dev/disk3s4     926Gi   641Mi   197Gi     1%     320  2.1G    0%   /System/Volumes/Update
/dev/disk1s2     500Mi   6.0Mi   480Mi     2%       1  4.9M    0%   /System/Volumes/xarts
/dev/disk1s1     500Mi   6.1Mi   480Mi     2%      28  4.9M    0%   /System/Volumes/iSCPreboot
/dev/disk1s3     500Mi   2.7Mi   480Mi     1%      82  4.9M    0%   /System/Volumes/Hardware
/dev/disk3s5     926Gi   703Gi   197Gi    79%    2.0M  2.1G    0%   /System/Volumes/Data
map auto_home      0Bi     0Bi     0Bi   100%       0     0     -   /System/Volumes/Data/home
/dev/disk3s1     926Gi    13Gi   197Gi     6%    404k  2.1G    0%   /System/Volumes/Update/mnt1
```

## 1) 각 항목의 의미

- `Filesystem`: 파일 시스템의 이름.
- `Size`: 파일 시스템의 전체 크기.
- `Used`: 사용된 공간.
- `Avail`: 사용 가능한 공간.
- `Capacity`: 사용된 용량의 백분율.
- `iused`: 사용된 인덱스 노드 수.
- `ifree`: 사용 가능한 인덱스 노드 수.
- `%iused`: 사용된 인덱스 노드의 백분율.
- `Mounted on`: 파일 시스템이 mount된 위치.

## 2) 각 Filesystem의 의미

- `/dev/disk3s1s1`: root 파일 시스템으로, 모든 주요 시스템 파일과 사용자 파일이 이곳에 저장된다.
- `devfs`: 장치 파일 시스템으로, 시스템에 연결된 장치들의 정보를 포함.
- `/dev/disk3s6`: 가상 메모리 볼륨으로 사용된다.
- `/dev/disk3s2`: 시스템의 부팅에 필요한 파일을 저장하는 preboot 볼륨.
- `/dev/disk3s4`: 시스템 업데이트에 사용되는 볼륨.
- `/dev/disk1s2`: xarts 볼륨, 일반적으로 애플 시스템에서 사용되는 임시 파일 시스템.
- `/dev/disk1s1`: iSCPreboot 볼륨, 시스템 부팅과 관련된 정보를 포함.
- `/dev/disk1s3`: 하드웨어 관련 파일을 저장하는 볼륨.
- `/dev/disk3s5`: 사용자 데이터와 애플리케이션 데이터가 저장되는 주요 데이터 볼륨.
- `map auto_home`: 네트워크를 통해 자동으로 mount되는 홈 디렉토리.
- `/dev/disk3s1`: 시스템 업데이트 과정에서 사용되는 임시 mount 지점.

<br>

# 3. 인덱스 노드(Inode)

파일 시스템에서 인덱스 노드(Inode)는 파일이나 디렉토리에 대한 metadata를 저장하는 데이터 구조이다. <br>
Inode는 파일의 크기, 저장 위치, 권한, 소유자 등의 중요한 정보를 포함하고 있다.

## 1) 인덱스 노드의 역할

- 메타데이터 저장: 파일의 크기, 소유자, 권한, 저장 위치 등을 저장.
- 파일 관리: 파일 시스템에서 각 파일과 디렉토리를 고유하게 식별하고 관리.

## 2) 인덱스 노드 비유

- 책 = 파일:
  - 파일은 도서관의 책과 같습니다. 파일에는 우리가 저장하고 싶은 데이터가 담겨 있다.
- 카탈로그 = 인덱스 노드(Inode):
  - 인덱스 노드는 도서관의 카탈로그에 비유 가능. 카탈로그는 책의 제목, 저자, 위치 등의 정보를 기록하고, 인덱스 노드는 파일의 메타데이터를 저장한다.

## 3) 파일을 찾는 과정 비유

- 도서관에서 책을 찾는 과정:
  - 카탈로그에서 책의 제목을 검색하고, 서가 번호를 찾아 책을 읽는다.
- 파일 시스템에서 파일을 찾는 과정:
  - 디렉토리에서 파일의 인덱스 노드를 검색하고, 메타데이터를 통해 파일이 저장된 위치를 찾아 내용을 읽는다.

## 4) 요약!

- 도서관의 책: 파일 시스템의 파일.
- 도서관의 카탈로그: 파일 시스템의 인덱스 노드(Inode).
- 책의 위치를 찾는 과정: 파일의 데이터를 찾는 과정.

<br>

즉, 인덱스 노드는 파일의 메타데이터를 관리하여 파일 시스템의 효율적인 운영을 가능하게 한다. <br>
이를 통해 파일 시스템은 각 파일과 디렉토리를 정확히 식별하고 필요한 데이터를 빠르게 접근할 수 있다.
