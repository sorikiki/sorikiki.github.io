---
layout: post
title: 'Git의 개념과 유용한 커밋 명령어 모음'
author: 'Dasol Kim'
tags: Tale
---

<p>단언컨대 개발자가 깃에 대해 공부하고 이를 숙지하는 것은 필수사항이다. 프로그래밍을 하여 완성된 <strong>결과</strong>물뿐만 아니라 그 <strong>과정</strong>에서도 우리의 프로그램이 발생할 수 있는 에러가 최소화된 안정된 상태를 유지하는 것은 개발자의 몫이다. 이러한 작업을 도와주는 유용한 도구는 오늘 배울 분산형 버전 관리 시스템(Distributed Version Control System)의 일종인 Git이다. 깃의 명령어를 입력할 터미널 공간이 필요한데, 필자는 깃의 전용 터미널인 Git Bash를 이용할 것이다. 명령어를 직접 입력하지 않고 쉽게 소스코드를 관리할 수 있는 Git GUI(Graphic User Inerface)로서 대표적으로 GitHub Desktop이나 소스트리를 이용해도 되지만, 깃의 내적 원리를 이해하고 소스코드에 대한 좀 더 정밀한 작업을 하기 위해서는 터미널을 이용하여 깃 명령어를 직접 입력하며 개발하는 것을 추천한다. 

<p>앞으로 깃 매뉴얼에 대한 포스팅을 추가적으로 올릴 예정이니 이번 포스팅에서는 오로지 깃의 동작 원리와 유용한 커밋 명령어들을 다루는 데에 초점을 맞출 것이다.   </p>
<br>
<br>
<h1>깃의 동작 원리</h1>
<p>깃의 논리적인 공간은 크게 다음과 같이 나눌 수 있다.
<ul>
    <li>워킹 디렉터리</li>
    <li>스테이지</li>
    <li>저장소</li>
</ul>

<p><strong>워킹 디렉터리</strong>는 실제로 개발자가 작업하는 공간이자 물리적으로 생성한 폴더라고 할 수 있다.워킹 디렉터리 안에는 소스코드가 담긴 파일들이 존재할 수 있다.</p>

<p>한편, 깃은 시스템 부하를 방지하기 위해 워킹 디렉터리 안에 있는 모든 파일들을 대상으로 변경 사항을 추적하지 않는다. 워킹 디렉터리 내부의 파일들은 깃의 감시 추적 유무에 따라 tracked 혹은 untracked 파일로 구분되는데, <strong>git add 파일명</strong>이라고 입력해주면 해당 파일을 untracked에서 tracked 상태로 변경해준다. 어떤 파일이 tracked 상태가 되면 그 파일은 <stronggit>스테이지</stronggit> 영역에 존재한다고 말한다.</p>
<p class="comment"><strong>git add . </strong>는 현재 경로의 디렉터리에 존재하는 모든 파일을 스테이지 영역으로 이동시킨다. 혹은 위의 경우처럼 디렉터리 내부의 특정 파일만 처리해줄 수도 있다.</p>

<p>워킹 디렉터리 역할을 할 폴더를 생성했다면(혹은 기존의 폴더를 이용해도 좋다.) <strong>git init</strong>이라는 초기화 명령어를 통해 깃 저장소의 지위를 부여해야한다. 여기에는 숨겨진 .git이라는 폴더가 생성되는데 이 폴더가 실질적인 <strong>저장소</strong>의 역할을 한다. 즉, 여기에 로컬 저장소에서 관리하는 모든 파일 및 브랜치의 변경 이력이 담긴다. 따라서 워킹 디렉터리 파일을 복사하거나 이동할 때 .git 폴더는 필수적으로 디렉터리 안에 포함되어야 한다. </p>

<p class="comment">참고로 <strong>cp -r 원본폴더 복사폴더</strong>는 깃 저장소와 .git을 통째로 특정 폴더에 복사할 수 있는 명령어이다. 일반적인 cp 명령어로는 .git 파일을 누락할 여지가 있다.</p>

<img src="/assets/post-image/git_image_1.png">

<p>워킹 디렉터리에서 스테이지 영역으로 파일의 상태를 변경하는 것을 <strong>등록</strong>이라고 부른다. 만일, 스테이지 영역에 등록된 파일이 변경되면 위와 같이 워킹 디렉터리에 돌아오는 untracked 상태가 된다. 이 파일을 다시 등록하고 싶다면, git add 명령어를 실행해주면 된다. 그림에서, 스테이지에 등록된 파일은 파란색으로 미등록된 파일은 노란색으로 표시하였다. </p>

<p>이렇게 스테이지에 등록된 파일들은 커밋될 수 있는 상태가 된다. <strong>커밋(commit)</strong>이란 코드의 변화를 기록하는 것으로, 깃에서 커밋은 코드의 전체 부분이 아니라 부모 커밋을 기준으로 새로 변경된 부분만 추출되어 이루어진다. 커밋은 <strong>순차적</strong>으로 이루어지며 한번 커밋된 내역은 <strong>영구적</strong>으로 저장소에 보관된다. 

<img src="/assets/post-image/git_image_2.png">

<p>깃의 커밋은 스냅샷 방식으로 이루어진다. 여기서 헤드란, 최종적인 커밋(=부모 커밋) 작업 위치를 가리키는 포인터 개념이다. 마치 스냅샷을 찍는 것처럼 새로 커밋을 하면 헤드가 가리키는 최종 커밋과 스테이지를 직접 비교하여 변경된 부분만 기록하게 된다. 이때 유의한 변화가 없다면 커밋을 할 수 없다. 커밋을 하고 나면 스테이지와 터미널에서 보여주는 작업 내역이 모두 초기화된다. 이렇게 누적된 커밋의 기록은 <strong>git log</strong> 명령어를 통해 커밋시 입력한 메시지와 함께 내림차순으로 확인할 수 있다. </p>
<br>
<br>
<h1>커밋 명령어 정리</h1>
<p><strong>git init</strong>: 깃 저장소로 초기화. 특정 경로에 있는 폴더를 깃이 관리하는 로컬 저장소로 만들어준다. 참고로 필요에 따라 폴더 이름은 자유롭게 변경할 수 있다.</p>
<p><strong>ls -a</strong>: 경로가 가리키는 디렉터리 내부에 있는 파일의 목록을 출력. -a 옵션을 붙이면 숨겨진 폴더, 예를 들면 .git까지 보여준다.</p>
<p><strong>cp -r 원본폴더 복사폴더: </strong>깃 저장소를 다른 폴더에 복사할 때 유용한 명령어. .git까지 통째로 복사하기 위해서는 -r 옵션도 반드시 추가해야 한다.</p>
<p><strong>git config user.name "사용자명" & git config user.email "이메일주소"</strong>: 커밋은 사용자 단위로 이루어진다. 따라서 지정된 사용자명과 이메일주소를 지정해주면 커밋 기록 확인시 유용하다. 혼자서 프로젝트를 만들 때보다는 협업할 때 더 빛을 발하는 명령어이다. (누가 언제 어떠한 변경을 했는 지 확인할 수 있으니까) 단, --global 옵션을 추가하면 로컬 저장소를 초기화할 때마다 사용자 세부사항을 지정하지 않고도 전역에서 관리하는 사용자 정보를 사용할 수 있다. </p>
<p><strong>git mv 파일명 새 파일명</strong>: 워킹 디렉터리에서 추적하는 파일 중 일부가 파일명이 변경될 필요가 있을 때 사용할 수 있는 명령어이다. 깃은 파일의 이름이 변경되어도 변경된 파일이 기존의 어떤 파일이 변경된 파일인지를 정확하게 알고 있다. </p>
<p><strong>git add 파일명</strong>: 워킹 디렉터리의 파일을 스테이지에 등록하는 명령어. add 뒤에 .을 사용하면 디렉터리 내부에 존재하는 모든 파일을 등록한다.</p>
<p><strong>git rm --cached 파일명</strong>: 스테이지에 등록된 파일을 untracked 상태로 바꾸는 명령문. 즉 해당 명령문은 워킹 디렉터리에 존재하는 해당 파일은 삭제하지 않는다. --cached 옵션을 제외하면 워킹 디렉터리와 커밋되어 저장소에 존재하는 파일까지 삭제한다. 깃에서 커밋 후 삭제 역시 유의한 변화이기 때문에 추가 커밋을 통해 이러한 변경사항을 적용시켜 최신으로 업데이트 해주어야 한다. </p>
<p><strong>git status</strong>: 깃의 상태를 확인하는 명령문. 신중하게 커밋하기 위해서는 항상 커밋 전후로 실행해보는 습관을 가질 필요가 있다. 커밋을 하면 'nothing to commit, working tree clean' 이라는 문구를 출력한다. </p>

<p><strong>git log (파일명)</strong>: 커밋 내역을 확인하는 명령문. 특정 파일명을 옵션으로 추가하면 해당 파일과 관련된 커밋 내역에 대해서만 보여준다. git status와 함께 항상 실행해볼 법한 명령문이다. 혹은 --pretty=short 옵션을 추가하면 커밋 메시지의 첫 줄만 간략하게 출력된 결과를 볼 수 있다. 커밋 메시지 입력 명령문에 관해서는 다음 줄에서 배운다.</p>

<p><strong>git commit</strong>: 이렇게 하면 vi에디터 창이 열린다. vi에디터를 사용하는 방법은 Esc + i를 누르면 커밋 메시지 입력이 가능하고 Esc + : + w + q를 누르면 커밋이 완료된다. 도중에 커밋을 안하고 싶으면 Esc + : + q를 눌러서 에디터를 빠져나올 수 있다. vi에디터를 사용할 때에는 한 줄만 입력해도 되지만, 여러 줄을 입력하고 싶은 경우에는 첫 줄은 주된 메시지(--pretty=short 옵션으로 보이는 부분)를, 한 줄 띄고 그 다음 줄부터는 세부적인 내용을 기록하는 것이 좋다.</p>

<p><strong>git commit -a</strong>: git commit에 -a 옵션을 추가하면 파일 등록과 커밋을 동시에 할 수 있다.</p>
<p><strong>git commit -m</strong>: git commit에 -m 옵션을 추가하면 커밋 메시지를 한 줄만 기록할 수 있다.</p>
<p><strong>git commit -am</strong>: -a 옵션과 -m 옵션을 동시에 사용하는 방법으로 저장소를 처음 생성하고 새로운 파일을 작성한 경우 해당 옵션은 사용할 수 없다.</p>

<p><strong>git commit --allow-empty-message -m ""</strong>: 커밋은 같은 파일에 대해 특정한 객체를 만드는 행위이기 때문에 반드시 커밋 메시지를 동반해야 한다. 그러나 경우에 따라서 빈 커밋 메시지를 입력해야 하는 경우라면, 이와 같은 명령어를 입력하면 된다. </p>

<p><strong>git commit --amend</strong>: 방금 직전 커밋한 메시지를 변경하고 싶을 때 사용하는 명령어. vi 에디터가 열리면 원하는 대로 커밋 메시지를 입력하면 된다.</p>
<p><strong>git show 커밋ID</strong>: 각각의 커밋은 해시 알고리즘에 의해 중복되지 않은 고유의 키를 부여받았다. 다음과 같이 이미 알고 있는 커밋ID를 이용하여 명령어를 입력하면 해당 커밋의 상세 정보를 확인할 수 있다.</p>

<p><strong>git diff</strong>: 스테이지에 파일을 등록하기 전 워킹 디렉터리의 수정 내역을 보여주는 명령어. git add하고 같은 명령어를 입력하면 아무것도 출력되지 않는다. </p>

<p><strong>git diff head</strong>: 스테이지화된 파일에서 HEAD가 가리키는 최신 커밋과 비교했을 때 변경된 내용을 보여주는 명령어.</p>

<p><strong>git commit -v</strong>: vi 에디터가 열리면 diff 내용(git diff head 명령문을 입력했을 때 출력하는 내용과 동일)을 함께 보며 커밋 메시지를 입력할 수 있도록 해주는 명령문. 단, 로그에는 diff 내용이 함께 기록되지 않는다. </p>

<p><strong>git checkout 파일명</strong>: 커밋 이후 수정된 해당 파일을 커밋 된 마지막 내용으로 되돌리는, 즉 변경된 사항을 삭제하는 명령어. </p>

<p>추가적으로 워킹 디렉터리 내부에 .gitignore(숨겨진 파일)에서 깃으로 관리하고 싶지 않은 파일과 폴더를 지정해 깃의 추적을 받지 않도록 설정해줄 수 있다. 이렇게 지정된 파일 혹은 폴더는 서버로 로컬 저장소를 복제할 때 분리하여 처리된다. </p>
<br>
<br>

<h1>마무리<h1>
<img src="/assets/post-image/git_image_3.png">
<br>
<p>다음 깃 포스팅에서는 로컬 저장소와 원격 저장소의 통신에 대해서 다룰 것이다. 여기서 통신은 소스코드의 복제를 의미한다. 굳이 로컬 저장소에서만 버전을 관리하지 않고 원격 저장소를 이용하는 이유는 로컬에서 작성한 코드를 백업하여 안정성을 높이고, 다른 개발자와 공유 및 협업하기 위함이고, 작업의 연속성을 보장하기 위함이다. 깃을 정복하기까지 얼마 남지 않았다! 조금만 더 화이팅 :)</p>
<br>

