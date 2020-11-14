====================
  Emacs 28.0.50
====================

왜?
이맥스 27.1 버전에선 company-box 패키지를 full-screen에서 사용하면 문제 생긴다.
(검은 창이 2개가 더 생기고 불안정한 모습을 보임)
구글링하다보니깐 이맥스 28에선 NSPort 이슈가 개선되면서 해결됐다는 글을 보았다.
(https://github.com/sebastiencs/company-box/issues/67 참고)

그런데?
맥의 이맥스를 제공하는 사이트들은 다 27버전 또는 그 이하더라.
결국 gnu에서 제공하는 최신 이맥스 버전의 소스코드를 다운해서 빌드하는게 최선의 방식이라고 판단.

--------------------

해결책
gnu 사이트에 들어가서 자신이 원하는 커밋 선택, 다운.
난 38e2a564d3b598edb04b6f737061a1df074f2d33,
위 이슈에서 언급한 그 커밋 사용함.
2020 3월 이맥스니 latest나 이후의 커밋 추천.

파일을 다운받으면 터미널로 들어가주고,
bash autogen.sh 로 configure 스크립트 생성,

./configure --with-ns --with-modules --enable-silent-rules \
     CC=clang OBJC=clang CFLAGS="-g -O3"
     
설정이 다 끝나면 make로 빌드.

_________ 수정 _________

make install 로 앱을 만들면 된다.
앱은 nextstep/ 에 있음.

_____ 아래는 무시하자 _____

빌드가 다 되면 cd nextstep/
여기에 Emacs.app 앱이 있을텐데, 열어보면 이상하게도 버전이 27로 실행된다.
Info.plist에는 버전이 28이 적혀있는데도

src/bootstrap-emacs | src/emacs | src/temacs
(셋의 파일 크기는 같고, 다 실행이 잘되는데 그 차이를 잘 모르겠다.)
위의 세 프로그램들은 실행시키면 버전이 28 버전으로 잘 뜬다.
이대로 사용해도 좋다, 하지만 앱으로 만들고 싶으니

복사를 하고, nextstep/Emacs.app/Contents/MacOS/ 에 Emacs.app으로 덮어씌우자.

여기가 끝이 아니다. nextstep/Emacs.app을 실행시키면 아직도 버전이 27임을 볼 수 있다.
28 버전의 프로그램으로 바꿔쳤는데도 말이다.
하지만 nextstep/.../MacOS/Emacs을 직접 실행시켜보면 여러 파일이 없어서 실행이 안된다.

원래 쓰던 Emacs에서, Emacs.app/Contents/MacOS/libexec 를 새로운 버전에 붙여넣어주자.
버전 27 사용자 기준에서, Emacs.app/Contents/MacOS/ 폴더 안에는
libexec (바로가기 폴더)
libexec-x86_64-10_10 (폴더)
libexec-x86_64-10_14 (폴더)

세 개의 폴더가 있을텐데,
바로가기 제외하고, 10_10과 10_14 두 개의 libexec폴더, Emacs 바이너리 파일도 보일 것이다.

정확히는 모르지만,
실행하면 10_10은 라이트 모드만, 10_14는 다크 모드도 지원하는 창으로 이맥스 프로그램이 실행된다.

아마 10_14가 10_10보다 높은 버전으로 보이니,
libexec-x86_64-10_14의 폴더를 새로운 이맥스 애플리케이션 폴더 안에 넣고,

Emacs.app/Contents/Resources/lisp
Emacs.app/Contents/Resource/etc

두 폴더도 북붙.

Emacs.app을 실행하면 잘 돌아간다.
