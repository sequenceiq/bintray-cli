: << USAGE
######################################################################
curl -Lo /tmp/bintray-functions j.mp/bintray-functions && . /tmp/bintray-functions
  or
wget -O /tmp/bintray-functions j.mp/bintray-functions && . /tmp/bintray-functions
######################################################################
USAGE

: ${BINTRAY_KEY:? required for authentication}
: ${BINTRAY_USER:? username is required}
: ${DEBUG:=1}

debug() {
  [ $DEBUG -gt 0 ] && echo [DEBUG] "$@" 1>&2
}

_replace_key() {
  sed s/$BINTRAY_KEY/\$BINTRAY_KEY/ <<< "$@"
}

_quote_spaces() {
  FULL=''
  for next in "$@"; do
    [[ "$next" == *\ * ]] && FULL="$FULL '$next'" || FULL="$FULL $next"
  done
  echo $FULL
}

run-debug() {
  quotedMsg=$(_quote_spaces "$@")
  safeMsg=$(_replace_key "$quotedMsg")
  debug "$safeMsg"

  bt_resp=$("$@" 2>/dev/null)
  echo $bt_resp
}

bint-curl() {
  path=$1
  # prefix with '/' if missing
  [ ${path:0:1} == "/" ] || path="/$path"
  shift
  run-debug curl "$@" -q -u $BINTRAY_USER:$BINTRAY_KEY https://api.bintray.com$path
}

bint-browse() {
  PS3="Your choice: "

  BINTRAY_SUBJECT=$1
  : ${BINTRAY_SUBJECT:? please specify username or organization}

  repos=$(bint-curl /repos/$BINTRAY_SUBJECT |jq .[].name -r)
  echo select a REPO
  select repo in $repos; do
    break
  done

  pacakages=$(bint-packages $BINTRAY_SUBJECT $repo)
  echo select a PACKAGE
  select package in $pacakages; do
    break
  done

  echo versions:
  bint-versions $BINTRAY_SUBJECT $repo $package
}

bint-last-response() {
  echo $bt_resp
}

bint-repos() {
  [ $# -gt 0 ] && BINTRAY_SUBJECT=$1
  : ${BINTRAY_SUBJECT:? please specify username or organization}
  bint-curl /repos/$BINTRAY_SUBJECT |jq .[].name -r
}

bint-packages() {
  BINTRAY_SUBJECT=$1
  BINTRAY_REPO=$2

  : ${BINTRAY_SUBJECT:? please specify username or organization}
  : ${BINTRAY_REPO:? repo is required}

  bint-curl /repos/$BINTRAY_SUBJECT/$BINTRAY_REPO/packages |jq .[].name -r
}

bint-package() {
  BINTRAY_SUBJECT=$1
  BINTRAY_REPO=$2
  BINTRAY_PACKAGE=$3

  : ${BINTRAY_SUBJECT:? please specify username or organization}
  : ${BINTRAY_REPO:? repo is required}
  : ${BINTRAY_PACKAGE:? package is required}

  bint-curl /packages/$BINTRAY_SUBJECT/$BINTRAY_REPO/$BINTRAY_PACKAGE
}

bint-versions() {
  bint-package "$@" | jq .versions[] -r
}

bint-create-version() {
  BINTRAY_SUBJECT=$1
  BINTRAY_REPO=$2
  BINTRAY_PACKAGE=$3
  BINTRAY_VERSION=$4

  : ${BINTRAY_SUBJECT:? please specify username or organization}
  : ${BINTRAY_REPO:? repo is required}
  : ${BINTRAY_PACKAGE:? package is required}
  : ${BINTRAY_VERSION:? version is required}

  bint-curl /packages/$BINTRAY_SUBJECT/$BINTRAY_REPO/$BINTRAY_PACKAGE/versions -H "Content-Type: application/json" -d @- <<EOF
{
"name": "$BINTRAY_VERSION",
"desc": "$BINTRAY_VERSION created"
}
EOF
}

bint-delete-version() {
  BINTRAY_SUBJECT=$1
  BINTRAY_REPO=$2
  BINTRAY_PACKAGE=$3
  BINTRAY_VERSION=$4

  : ${BINTRAY_SUBJECT:? please specify username or organization}
  : ${BINTRAY_REPO:? repo is required}
  : ${BINTRAY_PACKAGE:? package is required}
  : ${BINTRAY_VERSION:? version is required}

  bint-curl /packages/$BINTRAY_SUBJECT/$BINTRAY_REPO/$BINTRAY_PACKAGE/versions/$BINTRAY_VERSION -X DELETE
}

bint-upload() {
  BINTRAY_SUBJECT=$1
  BINTRAY_REPO=$2
  BINTRAY_PACKAGE=$3
  BINTRAY_VERSION=$4
  FILE=$5

  : ${BINTRAY_SUBJECT:? please specify username or organization}
  : ${BINTRAY_REPO:? repo is required}
  : ${BINTRAY_PACKAGE:? package is required}
  : ${BINTRAY_VERSION:? version is required}
  : ${FILE:? required}

  #versionedFileName=${FILE%%.*}-${BINTRAY_VERSION}.${FILE##*.}
  bint-curl /content/$BINTRAY_SUBJECT/$BINTRAY_REPO/$BINTRAY_PACKAGE/$BINTRAY_VERSION/ \
    --upload-file $FILE

  bint-curl /content/$BINTRAY_SUBJECT/$BINTRAY_REPO/$BINTRAY_PACKAGE/$BINTRAY_VERSION/publish \
    -X POST
}

bint-upload-with-version() {
  bint-delete-version "$@"
  bint-create-version "$@"
  bint-upload "$@"
}
