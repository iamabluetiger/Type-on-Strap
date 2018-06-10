---
layout: post
title: VIM 실사용기
date: 2016-09-16 17:20:02.000000000 +09:00
tags: [vim, sublime, text editor]
---

기억이 나지도 않는다. 내가 왜 갑자기 그 좋은 Sublime Text를 두고 **Vim**을 쓰기로 마음 먹었을까...? 

Vim을 사용한지 2~3달쯤 된 것 같은데 어떤 계기로 옮기게 된건지 정확히 모르겠다. 그냥 개발자로서 뭔가 간지나서 였는지, 생산성이 좋아진다는 말에 혹해서 였는지, 터미널 왔다갔다 하기 귀찮아서 였는지.. 어쨋든 결과적으로 꽤나 잘 사용하고 있으며 몇가지 불편함[^1]이 있는 경우에는 Sublime Text를 혼용하는 식으로 사용중이다.

여느 때와 다름 없이 환경설정을 어떻게 할 것인가가 가장 큰 고민이였고 가장 시간이 오래걸렸다. 먼저 Sublime Text를 사용하면서 정말 편하다고 생각했던 기능들을 나열하였다.

* Directory Tree Viewer (CMD + K, B)
* Search File (CMD + P)
* Automatically Complete Word
* Multiple Cursor
* Monokai Theme

다행히도 Vim을 사랑하는 개발자들이 Sublime Text가 지원하는 대부분을 구현시켜 놓았다. 없는것을 찾는게 더 힘들 정도.. 먼저 Vim에도 플러그인 패키지를 관리하는 [Vundle](https://github.com/VundleVim/Vundle.vim)이 있었고 덕분에 .vimrc 파일 하나 안에서 내가 원하는 플러그인 설치와 설정을 모두 끝마칠 수 있었다. 실제로 Vim을 한창 사용하고 있다가 데스크탑에 우분투를 설치하고 사용했던 적이 있는데 파일 하나만으로 텍스트 에디터 환경을 구축하니 굉장히 편했다. (사실 Sublime Text도 그럴 수 있겠지..껄껄)

어찌 됐든 위에 나열한 것들을 사용하기 위해 쓴 플러그인들을 순서대로 나열해보겠다.

* [The NERD Tree](https://github.com/scrooloose/nerdtree)
* [ctrlp](https://github.com/kien/ctrlp.vim)
* [AutoComplPop](https://github.com/vim-scripts/AutoComplPop)
* [vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors)
* [vim-monokai](https://github.com/crusoexia/vim-monokai)

위의 플러그인들을 사용하면 Vim의 생산성을 훨씬 더 높혀줄 수 있다. 특히 ctrlp는 파일 여기저기 다니며 개발하시는 분들에게 Directory Tree Viewer가 필요하지 않을만큼 강력한 플러그인이 아닌가 생각한다. 그리고 monokai는 사실 중요하지 않지만 개인적으로 가장 좋아하는 Theme이기에 끼워넣었다 😏 

하지만 multiple-cursors는 사용을 시도하다가 실패하였다. 이상한 에러가 계속 뜨고 ctrlp랑 충돌이 조금 일어나는 문제이지 않을까 예상하고 있다. 고치려고 하다가 그냥 Sublime Text랑 혼용하여 사용하면 될 것 같아 고치지는 않았다. 그럼에도 불구하고 추가한 이유는 이 기능이 워낙에 자주 쓰이기 때문에 사용에 성공하시는 분들은 꼭 사용하시길 바라기 때문이다. 😂

---

플러그인들을 설치하고 나면 Vim의 간단한 설정을 만져주고 키 매핑을 해줄 차례이다. 사실 이런 설정들은 개인의 취향에 맞게 하면 되는 부분이기에 찾아보고 직접 설정하는 것을 추천드린다.

```java
set nu
set smarttab
set smartindent
set tabstop=2
set shiftwidth=2 
set expandtab
set autowrite
set autoread
set smartcase
set clipboard=unnamed " This enable use system clipboard. You sholud upgrade vim before use

syntax enable
colorscheme monokai
" let g:molokai_original = 1
let g:rehash256 = 1
set guifont=Monaco:h18

" Use ctrl-N for NERDTree bar 
nmap <C-N> :NERDTreeToggle<CR>

" Use ctrl-[hjkl] to select the active split!
nmap <silent> <c-k> :wincmd k<CR> 
nmap <silent> <c-j> :wincmd j<CR>
nmap <silent> <c-h> :wincmd h<CR> 
nmap <silent> <c-l> :wincmd l<CR>

" User ctrl-+, ctrl-n to resize vertical resize
nmap <silent> 0 :vertical res+5<CR>
nmap <silent> 9 :vertical res-5<CR>
nmap <silent> 7 :res-5<CR>
nmap <silent> 8 :res+5<CR>
```

개인적으로 꼭 쓰라고 추천드리고 싶은 것은 split cursor 이동과 split 크기 조정에 키를 할당하는 것이다. ctrl-[hjkl]은 제가 사용하는 키매핑을 사용해도 무방하지만 7,8,9,0을 split 크기 조정에 사용하는 것은 라인 점프할 때 충돌이 일어나기 때문에 사실 조금 고민을 해볼 필요가 있다.~~저도 고민하고 있는데 어디다 할당하면 좋을까요..~~

---

실사용기라기보다는 Vim 설정법에 가까운 이야기가 되어버렸다. 그렇지만 플러그인, 설정에 대한 이야기를 제외하면 할 이야기가 없기에.. 이야기 하는 김에 여기 저장해놓는 것도 좋을 듯 하고 😁 

Vim 자체가 터미널에서 돌아가다보니 용량이 큰 텍스트를 다룰 때는 정말 극악의 성능으로 Sublime Text로 돌아가고싶은 욕구가 샘솟는다. 그러나 기본 Terminal을 쓰다가 iTerm으로 넘어가서 조금**_정말조금_** 나아진 성능 향상을 맛보았고 bash를 화면 이동 없이 사용한다는 개인적으로 너무나 큰 장점때문에 돌아가지는 못하고 있다. 그리고 Vim의 자랑인 단축키에도 익숙해져 막힘없는 타이핑을 즐기고 있기 때문에[^2] 더더욱 돌아가지 못할 것 같다.....

는 가끔 Sublime Text로 개발을 하면 하.. 정말 빠르고**_빠르고빨라서_** 고민이 많긴 하다. Vintage 모드를 사용하면 두마리 토끼를 다 잡는 느낌이기도할 것 같은데.. 뭔가 잡종을 쓰는 기분이라 😥 ~~아니면 아예 Atom으로 가버릴까~~ 

어쨋든 당분간은 Vim을 계속 쓸 듯 하다. 만약 Vintage 모드를 사용하게 되면 간단한 후기를 남기겠다!

---

[^1]: Multi cursor가 필요할 때, Text가 많은 경우에 주로 Sublime을 사용한다.
[^2]: 하지만 이 경우는 Sublime Text도 [Vintage](https://www.sublimetext.com/docs/2/vintage.html)모드를 지원해 Vim만의 자랑은 아니다.
