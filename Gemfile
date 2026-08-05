# 로컬 미리보기용. GitHub Pages는 이 파일을 보지 않고 자체 환경에서 빌드하므로,
# 여기 목적은 "운영과 같은 조합을 로컬에서 재현하는 것" 하나입니다.
#
# jekyll을 직접 지정하지 않고 github-pages gem을 쓰는 이유:
# _config.yml의 remote_theme(minimal-mistakes)은 jekyll-remote-theme 플러그인이
# 있어야 동작하는데, 이 플러그인은 _config.yml의 plugins 목록에 없습니다.
# GitHub Pages가 자체적으로 넣어주기 때문입니다. github-pages gem이 그 조합을
# 통째로 가져오므로, 순정 jekyll로 빌드할 때처럼 테마가 통째로 빠지는 일이 없습니다.
#
# Ruby는 Homebrew의 ruby@3.3을 씁니다. GitHub Pages 빌드 환경이 3.3이고,
# 시스템 ruby(2.6)와 최신 ruby(4.x) 양쪽 다 github-pages gem이 물고 오는
# jekyll 3.10과 맞지 않습니다. keg-only라 PATH 앞에 직접 붙여야 합니다.
#
# LC_ALL을 지정하는 이유: 안 주면 sass 변환기가 파일을 US-ASCII로 읽어서
# main.scss의 한글 주석에서 "Invalid US-ASCII character" 에러가 납니다.
#
# 실행:
#   export PATH="/usr/local/opt/ruby@3.3/bin:$PATH"
#   export LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
#   bundle install
#   bundle exec jekyll serve   # http://localhost:4000

source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

# Ruby 3.0부터 webrick이 표준 라이브러리에서 빠져서, jekyll serve에 필요합니다.
gem "webrick", "~> 1.8"
