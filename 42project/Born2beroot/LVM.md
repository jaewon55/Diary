# LVM
## Partition
#### Partition?
+ **partition : 하나의 물리적인 디스크를 여러개의 논리적 디스크로 나누는 것**
+ **파티션의 종류**
    + 주(Parimary) 파티션 : 4개까지 생성 가능한 기본 파티션
    + 확장(Extended) 파티션 : 4개 이상 필요할 경우 생성
    + 논리(Logical) 파티션 : 확장 파티션을 다시 나눈 파티션
+ **모든 파티션은 하나의 파일 시스템을 가지고 있어야 한다.**
****
## LVM
#### PV,PE,VG,LV,LE
+ **5개의 용어들은 모두 LVM에서 사용하기 위한 개념이다.**
+ **PE(Physical Extent) : LVM에서 디스크의 공간을 나누는 최소 단위(4MB)**
+ **PV(Physical Volume) : PE들로 구성된 그룹**
+ **VG(Volume Group) : PV들의 집합**
+ **LV(Logical Volume) : VG를 논리적인 공간으로 바꾸어 사용자가 최종적으로 다루게 되는 공간**
+ **LE(Logical Extent) : LV를 구성하는 단위로 PE와 1:1로 대응된다.**

#### LVM?
+ **LVM(Logical Volume Manager)은 Logical Volume을 효율적으로 관리하기 위한 프로그램**
+ **디스크를 사용중 하나의 논리 파티션의 공간이 부족해 지면 다른 공간(LE)을 가져와 추가할 수 있기 때문에 공간을 효율적으로 관리할 수 있다.**
****