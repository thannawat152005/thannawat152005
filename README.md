#!/usr/bin/env bash
## Copyright 2017-2022 (c) by SDRausty, all rights reserved, see LICENSE
## hosting termuxarch.github.io/TermuxArch courtesy pages.github.com
## termuxarch.github.io/TermuxArch/CONTRIBUTORS thank you for your help!
################################################################################
set -Eeuo pipefail
shopt -s  extglob nullglob globstar
unset LD_PRELOAD
VERSIONID=2.1.882
_STRPEROR_() { # run on script error
local RV="$?"
printf "\\e[1;48;5;138m %s" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} NOTICE:  Generated script signal received ${RV:-UNKNOWN} near or at line number ${1:-UNKNOWN} by '${2:-UNKNOWNCOMMAND}'!  "
[ $(ifconfig 2>/dev/null | grep inet | wc -l) = 1 ] && printf "\\e[1;48;5;133m %s" "Please ensure background data is not restricted.  Check the wireless connection.  "
exit "$RV"
}
_STRPEXIT_() { # run on exit
local RV="$?"
if [[ -n "${TAMATRIXENDLCR:-}" ]]
then
_TAMATRIXEND_
fi
if [[ "$RV" != 0 ]]
then
_PTSTRPXT_
fi
if [[ "$RV" = 0 ]]
then
printf "\\e[0;32mCommand \\e[1;32m'%s' \\e[0;32mversion %s\\e[1;34m: \\e[1;32m%s\\n" "${STRNRG:-}" "${VERSIONID:-}" "DONE 🏁 "
printf "\033]2;%s\\007" "${STRNRG:-}:  DONE 🏁 "
else
printf "\\e[0;32mCommand \\e[1;32m'%s' \\e[0;32mversion %s\\e[1;34m: \\e[1;32m%s\\n" "${STRNRG:-}" "${VERSIONID:-}" "[Exit Signal $RV] DONE 🏁 "
printf "\033]2;%s\\007" "${STRNRG:-} [Exit Signal $RV]:  DONE 🏁 "
fi
[ -z "${TAMPDIR:-}" ] || rm -rf "$TAMPDIR"
printf "\\e[?25h\\e[0m"
set +Eeuo pipefail
}
_STRPHNGP_() { # run on hang up
local RV="$?"
printf "\\e[1;48;5;138m %s" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} HANG UP:  Generated signal received ${RV:-UNKNOWN} near or at line number ${1:-UNKNOWN} by '${2:-UNKNOWNCOMMAND}'!  "
exit "$RV"
}
_STRPNTRT_() { # run on signal
local RV="$?"
printf "\\e[1;48;5;138m%s" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} SIGNAL:  Generated signal received ${RV:-UNKNOWN} near or at line number ${1:-UNKNOWN} by '${2:-UNKNOWNCOMMAND}'!  "
exit "$RV"
}
_STRPQUIT_() { # run on quit
local RV="$?"
printf "\\e[1;48;5;138m %s" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} QUIT:  Quit signal received ${RV:-UNKNOWN} near or at line number ${1:-UNKNOWN} by '${2:-UNKNOWNCOMMAND}'!  "
exit "$RV"
}
_STRPTERM_() { # run on terminate
local RV="$?"
printf "\\e[1;48;5;138m %s" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} TERMINATE:  Generated signal received ${RV:-UNKNOWN} near or at line number ${1:-UNKNOWN} by '${2:-UNKNOWNCOMMAND}'!  "
exit "$RV"
}
trap '_STRPEROR_ $LINENO $BASH_COMMAND $?' ERR
trap '_STRPEXIT_ $LINENO $BASH_COMMAND $?' EXIT
trap '_STRPHNGP_ $LINENO $BASH_COMMAND $?' HUP
trap '_STRPNTRT_ $LINENO $BASH_COMMAND $?' INT
trap '_STRPQUIT_ $LINENO $BASH_COMMAND $?' QUIT
trap '_STRPTERM_ $LINENO $BASH_COMMAND $?' TERM
ARGS="${@%/}"
PGNM="${0##*/}"
{ [ -z "${ARGS:-}" ] && STRNRG="${0##*/}" ; } || STRNRG="${0##*/} ${ARGS:-}"
if [ "$EUID" = 0 ] || [ "$UID" = 0 ]
then
printf "\\e[1;48;5;168m%s\\e[0m  " "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} SIGNAL:  Please do not use the root login for PRoot:  EXITING..." && exit
fi
if [ -w /root ]
then
printf "\\e[1;48;5;138m%s\\e[0m  " "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} SIGNAL:  Please run '${0##*/}' and 'bash ${0##*/}' from the BASH shell in native Termux:  EXITING..." && exit
fi
_ARG2DIR_() {  # argument as ROOTDIR
ARG2="${@:2:1}"
if [[ -z "${ARG2:-}" ]]
then
ROOTDIR=/arch
else
ROOTDIR=/"$ARG2"
fi
_PREPTERMUXARCH_
}
_CHK_() {
if sha512sum -c --quiet termuxarchchecksum.sha512
then
if [[ -z "${INSTALLDIR:-}" ]]	# is unset
then	# exit here or the program will run on
printf "\\e[0;34m%s \\e[1;34m%s \\e[1;32m%s\\n" " 🕛 = 🕛" "TermuxArch version $VERSIONID integrity:" "OK"
exit 123
else
printf "\\n\\e[0;34m%s \\e[1;34m%s \\e[1;32m%s\\n" " 🕛 > 🕜" "TermuxArch version $VERSIONID integrity:" "OK"
_CHKSELF_
_COREFILESLOAD_
fi
else
printf "\\n"
_PRINTSHA512SYSCHKER_
fi
}
_CHKDWN_() {
sha512sum -c --quiet setupTermuxArch.sha512 && printf "\\e[0;34m%s\\e[1;34m%s\\e[1;32m%s\\n" " 🕛 > 🕐 " "TermuxArch download: " "OK" || _PRINTSHA512SYSCHKER_
TMPCMD="bsdtar -x -p -f setupTermuxArch.tar.gz" && $TMPCMD || printf "\\e[1;48;5;138m%s\\e[0m  " "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} SIGNAL $TMPCMD:  CONTINUING..."
}
_CHKSELF_() {	# compare setupTermuxArch and file being used
cd "$WFDIR"	# change directory to working file directory
if [[ "$(<"$TAMPDIR"/setupTermuxArch)" != "$(<"${0##*/}")" ]] # differ
then	# update the working file to newest version
## update working file
cd "$WDIR"
cp "$TAMPDIR/setupTermuxArch" "$0"
[[ -z "${ARGS:-}" ]] && printf "\\n\\e[1;32mFile \\e[0;32m'%s'\\e[1;32m was UPDATED\\e[1;34m:\\e[0;32m  Please run 'bash %s' again if this automatic update was unsuccessful.\\n\\n\\e[1;32mRESTARTED \\e[0;32m'%s'\\e[1;34m:\\e[1;32m CONTINUING...\\n\\n" "${0##*/}" "${0##*/}" "${0##*/}" || printf "\\n\\e[1;32mFile \\e[0;32m'%s'\\e[1;32m was UPDATED\\e[1;34m:\\e[0;32m run 'bash %s' again if this automatic update was unsuccessful;  You should be able to use the '!!' command to run '%s' again.\\n\\e[1;32mRESTARTED \\e[0;32m'%s'\\e[1;34m:\\e[1;32m CONTINUING...\\n\\n" "${0##*/}" "$STRNRG" "$STRNRG" "$STRNRG"
## restart with updated version
. $0 $ARGS
fi
cd "$TAMPDIR"
}
_CHOOSEABI_(){
if [[ -z "$CPUABILIST64" ]]
then
ARCHITEC="i386"
CPUABI="i386"
else
ARCHITEC="x86_64"
CPUABI="x86_64"
fi
}
_CHOOSEABIx86_(){
CPUABILIST64="$(getprop ro.product.cpu.abilist64)"
if [[ $CPUABI == *86* ]]
then
_OPT1_ "$@"
_INTRO_ "$@"
else
_CHOOSEABI_
_OPT1_ "$@"
_QEMU_
_INTRO_ "$@"
fi
}
_COREFILES_() {
[[ -f archlinuxconfig.bash ]] && [[ -f espritfunctions.bash ]] && [[ -f fbindsfunctions.bash ]] && [[ -f getimagefunctions.bash ]] && [[ -f initkeyfunctions.bash ]] && [[ -f knownconfigurations.bash ]] && [[ -f maintenanceroutines.bash ]] && [[ -f necessaryfunctions.bash ]] && [[ -f printoutstatements.bash ]] && [[ -f setupTermuxArch ]]
}
_COREFILESDO_() {
cd "$WFDIR" || exit 169	# change directory to working file directory
if _COREFILES_
then
LOADLCRFILES=0 && _COREFILESLOAD_
else
cd "$TAMPDIR"
_DWNL_
_CHKDWN_
_CHK_ "$@"
fi
}
_COREFILESLOAD_() {
if [[ "$OPT" = BLOOM ]]
then
rm -f termuxarchchecksum.sha512
fi
if [[ "$OPT" = MANUAL ]]
then
_MANUAL_
fi
. necessaryfunctions.bash
_LOADCONF_
. fbindsfunctions.bash
. initkeyfunctions.bash
. maintenanceroutines.bash
. archlinuxconfig.bash
. espritfunctions.bash
. getimagefunctions.bash
. printoutstatements.bash
}
_DEPENDDM_() { # check and set download manager
for PKG in "${!ADM[@]}"
do
if [[ -x $(command -v "${ADM[$PKG]}") ]]
then
DM="$PKG"
printf "\\nFound download tool '%s': Continuing...\\n" "$PKG"
break
fi
done
}
_DEPENDIFDM_() { # check if download tool is available and set for install
for PKG in "${!ADM[@]}" # check from available toolset and set one for install
do #	check for both set DM and if tool exists on device
if [[ "$DM" = "$PKG" ]] && [[ ! -x $(command -v "${ADM[$PKG]}") ]]
then	#	sets both download tool for install and exception check.
PKGS+=($PKG)
printf "\\nSetting download tool '%s' for install: Continuing...\\n" "$PKG"
fi
done
}
_DEPENDS_() {	# check for missing commands
_INPKGS_() {	# install missing packages
STRNGB="\\e[1;38;5;146m%s"
STRNGC="\\e[1;38;5;124m%s"
if [[ "$COMMANDIF" = au ]] # can enable rollback https://wae.github.io/au/
then	# use 'au' to install missing packages
au "${PKGS[@]}" && printf "$STRNGB%s" "$STRING1F" || printf "$STRNGC%s" "$STRING2"
elif [[ "$COMMANDIF" = pkg ]]
then	# use 'pkg' to install missing packages
pkg install "${PKGS[@]}" printf '%s' "$STRNGC $STRING1" || printf '%s' "$STRNGC $STRING2"
elif [[ "$COMMANDIF" = apt ]]
then	# use 'apt' to install missing packages
apt install "${PKGS[@]}" --yes && printf "$STRNGB%s" "$STRING1" || printf "$STRNGC%s" "$STRING2"
fi
}
if [[ -z "${VLORALCR:-}" ]]
then
PKGS=(bsdtar proot)
else
PKGS=(pulseaudio bsdtar proot)
fi
printf "\\e[1;34mChecking prerequisites...\\n\\e[1;32m"
ADM=([aria2]=aria2c [axel]=axel [curl]=curl [lftp]=lftpget [wget]=wget)
if [[ "$DM" != "" ]]
then
_DEPENDIFDM_
fi
if [[ "$DM" = "" ]]
then
_DEPENDDM_
fi
## set and install lftp if nothing else was found
if [[ "$DM" = "" ]]
then
DM=lftp
PKGS+=(lftp)
printf "Setting download tool 'lftp' for install: Continuing...\\n"
fi
for PKG in "${PKGS[@]}"
do	# check for missing commands
COMMANDP="$(command -v "$PKG")" || printf "\\e[1;38;5;242mCommand %s not found: Continuing...\\e[0m\\n" "$PKG" # test if command exists
COMMANDPF="${COMMANDP##*/}"
if [[ "$COMMANDPF" != "$PKG" ]]
then
_INPKGS_
fi
done
printf "\\n\\e[1;38;5;242mUsing %s to manage downloads.\\e[0m\\n" "${DM:-lftp}"
printf "\\n\\e[0;34m 🕛 > 🕧 \\e[1;34mPrerequisites: \\e[1;32mOK  \\e[1;34mDownloading TermuxArch...\\n\\n\\e[0;32m"
}
_DEPENDSBLOCK_() {
_DEPENDS_ || _PSGI1ESTRING_ "_DEPENDS_ _DEPENDSBLOCK_ ${0##*/}"
_COREFILESDO_ "$@"
}
_DWNL_() { # download TermuxArch from Github
FILE[sha]="https://raw.githubusercontent.com/TermuxArch/TermuxArch/master/setupTermuxArch.sha512"
FILE[tar]="https://raw.githubusercontent.com/TermuxArch/TermuxArch/master/setupTermuxArch.tar.gz"
if [[ "$DM" = aria2 ]]
then	# use https://github.com/aria2/aria2
"${ADM[aria2]}" -Z "${FILE[sha]}" "${FILE[tar]}"
elif [[ "$DM" = axel ]]
then	# use https://github.com/mopp/Axel
"${ADM[axel]}" -a "${FILE[sha]}"
"${ADM[axel]}" -a "${FILE[tar]}"
elif [[ "$DM" = curl ]]
then	# use https://github.com/curl/curl
"${ADM[curl]}" "$DMVERBOSE" -O {"${FILE[sha]},${FILE[tar]}"}
elif [[ "$DM" = wget ]]
then	# use https://github.com/mirror/wget
_DOADMWGET_() {
"${ADM[wget]}" "$DMVERBOSE" -N --show-progress "${FILE[sha]}" "${FILE[tar]}"
}
_DOADMWGET_  || (au wget && "$PREFIX/bin/wget" "$DMVERBOSE" -N --show-progress "${FILE[sha]}" "${FILE[tar]}") || _PSGI1ESTRING_ "_DOADMWGET_ _DWNL_ ${0##*/}"
else	# use https://github.com/lavv17/lftp
"${ADM[lftp]}" "${FILE[sha]}" "${FILE[tar]}"
fi
}
_EDITORCHOOSER_() {	# add 'export EDITOR=editor_name' to HOME/.bash_profile in order to use your favorite editor during runtime
if [[ -z "${EDITOR:-}" ]]
then
if command -v editor 1>/dev/null
then
USEREDIT="editor"
fi
elif [[ -n "${EDITOR:-}" ]]
then
USEREDIT="$EDITOR"
fi
if [[ -z "${EDITOR:-}" ]]
then
USEREDIT="nano"
fi
}
_INTRO_() {
printf "\033]2;%s\007" "bash $STRNRG 📲"
_SETROOT_EXCEPTION_
_INSTLLDIRCHK_
_PRINTINTRO_ "will attempt to install Linux in " "~/${INSTALLDIR##*/}" ".  Arch Linux in TermuxArch PRoot QEMU will be available upon successful completion"
_DEPENDSBLOCK_ "$@"
if [[ "$LCC" = "1" ]]
then
_LOADIMAGE_ "$@"
else
_MAINBLOCK_
fi
}
_INTROBLOOM_() { # BLOOM = setupTermuxArch manual verbose
OPT=BLOOM
printf "\033]2;%s\007" "bash ${0##*/} bloom 📲"
_PRINTINTRO_ "bloom option" "" ""
_PREPTERMUXARCH_
_DEPENDSBLOCK_ "$@"
_BLOOM_
}
_INTROSYSINFO_() {
printf "\033]2;%s\007" "bash ${0##*/} sysinfo 📲"
_SETROOT_EXCEPTION_
_PRINTINTRO_ "will create a system information file" "" ""
_DEPENDSBLOCK_ "$@"
_SYSINFO_ "$@"
}
_DODIRCHK_() {
_SETROOT_EXCEPTION_
if [ ! -d "$INSTALLDIR" ] || [ ! -d "$INSTALLDIR/root/bin" ] || [ ! -d "$INSTALLDIR/var/binds" ] || [ ! -f "$INSTALLDIR/bin/we" ] || [ ! -f "$INSTALLDIR/usr/bin/env" ]
then
printf "\\n\\e[0;33m%s\\e[1;33m%s\\e[0;33m.\\n\\n" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} NOTICE!  " "The root directory structure is of ~/${INSTALLDIR##*/} seems to be incorrect; Cannot continue '$STRNRG'!  This command '${0##*/} help' has more information"
if [ -d "$INSTALLDIR"/tmp ]
then	# check for superfluous tmp directory
DIRCHECK=0
DIRNAME=(dev etc home opt proc root sys usr var)
for IDIRNAME in ${DIRNAME[@]}
do
if [ ! -d "$INSTALLDIR/$IDIRNAME" ]
then
DIRCHECK=1
else
DIRCHECK=0
fi
done
fi
if [ -z "${DIRCHECK:-}" ]
then
printf "Variable DIRCHECK is unbound.\\n"
elif [ "$DIRCHECK" -eq 1 ]
then	# delete superfluous tmp dir
rm -rf "$INSTALLDIR"/tmp
rmdir "$INSTALLDIR" ||  _PSGI1ESTRING_ "rmdir INSTALLDIR _DODIRCHK_ ${0##*/}"
fi
exit 204
fi
}
_INTROREFRESH_() {
printf '\033]2;  bash setupTermuxArch refresh 📲 \007'
if [ "${OPT:-}" = FORCE ]
then
_DODIRCHK_
else
_SETROOT_EXCEPTION_
fi
_PRINTINTRO_ "will refresh your TermuxArch files in " "~/${INSTALLDIR##*/}" ".  Arch Linux in TermuxArch PRoot QEMU will be available upon successful completion"
_DODIRCHK_
_DEPENDSBLOCK_ "$@"
_REFRESHSYS_ "$@"
}
_INSTLLDIRCHK_() {
if [[ -f "$INSTALLDIR"/bin/we ]] && [[ -d "$INSTALLDIR"/usr/local/termuxarch/bin ]] && [[ -d "$INSTALLDIR"/root/bin ]] && [[ -d "$INSTALLDIR"/var/binds ]]
then
printf "\\n\\e[0;33m%s\\e[1;33m%s\\e[0;33m.\\n\\n" "ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} NOTICE!  " "The root directory structure of ~/${INSTALLDIR##*/} appears correct;  Cannot continue '$STRNRG' to install Arch Linux in TermuxArch PRoot QEMU!  Commands '${0##*/} h[e[lp]]' have more information"
exit 205
fi
}
_LOADCONF_() {
if [[ -f "${WDIR}setupTermuxArchConfigs.bash" ]]
then
. "${WDIR}setupTermuxArchConfigs.bash"
_PRINTCONFLOADED_
else
. knownconfigurations.bash
fi
}
_MANUAL_() {
printf '\033]2; bash setupTermuxArch manual 📲 \007'
if [[ -f "${WDIR}setupTermuxArchConfigs.bash" ]]
then
$USEREDIT "${WDIR}setupTermuxArchConfigs.bash"
else
cp knownconfigurations.bash "${WDIR}setupTermuxArchConfigs.bash"
sed -i "7s/.*/\# The architecture of this device is $CPUABI; Adjust configurations in the appropriate section.  Change mirror (https:\/\/wiki.archlinux.org\/index.php\/Mirrors and https:\/\/archlinuxarm.org\/about\/mirrors) to desired geographic location to resolve 404 and checksum issues.  /" "${WDIR}setupTermuxArchConfigs.bash"
$USEREDIT "${WDIR}setupTermuxArchConfigs.bash"
fi
}
_NAMEINSTALLDIR_() {
if [[ "$ROOTDIR" = "" ]]
then
ROOTDIR=arch
fi
INSTALLDIR="$(printf "%s\\n" "$HOME/${ROOTDIR%/}" | sed 's#//*#/#g')"
}
_NAMESTARTARCH_() {
DARCH="$(printf "%s\\n" "${ROOTDIR%/}"|sed 's#//*#/#g')" # ${@%/} removes trailing slash
if [[ "$DARCH" = "/arch" ]]
then
AARCH=""
STARTBI2=arch
else
AARCH="$(printf "%s\\n" "$DARCH" | sed 's/\//\+/g')"
STARTBI2=arch
fi
STARTBIN=start"$STARTBI2$AARCH"
}
_OPT1_() {
if [[ -z "${2:-}" ]]
then
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
elif [[ "${2//-}" = [Bb]* ]]
then
shift
printf "%s\\n" "Setting mode to bloom."
_INTROBLOOM_ "$@"
elif [[ "${2//-}" = [Dd]* ]] || [[ "${2//-}" = [Ss]* ]]
then
shift
printf "%s\\n" "Setting mode to sysinfo."
_ARG2DIR_ "$@"
_INTROSYSINFO_ "$@"
elif [[ "${2//-}" = [Ii]* ]]
then
shift
printf "%s\\n" "Setting mode to install."
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
elif [[ "${2//-}" = [Mm][Ii]* ]]
then
shift
printf "%s\\n" "Setting mode to manual install."
OPT=MANUAL
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
elif [[ "${2//-}" = [Mm]* ]]
then
shift
printf "%s\\n" "Setting mode to manual."
OPT=MANUAL
_OPT2_ "$@"
elif [[ "${2//-}" = [Rr][Ee][Ff][Rr][Ee]* ]]
then
shift
printf "\\nSetting mode to full refresh.\\n"
_PRPREFRESH_ "5"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${2//-}" = [Rr][Ee][Ff][Rr]* ]]
then
shift
printf "\\nSetting mode to 4 refresh.\\n"
_PRPREFRESH_ "4"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${2//-}" = [Rr][Ee][Ff]* ]]
then
shift
printf "\\nSetting mode to 3 refresh.\\n"
_PRPREFRESH_ "3"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${2//-}" = [Rr][Ee]* ]]
then
shift
printf "\\nSetting mode to 2 refresh.\\n"
_PRPREFRESH_ "2"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${2//-}" = [Rr]* ]]
then
shift
printf "%s\\n" "Setting mode to 1 refresh."
_PRPREFRESH_ "1"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
else
_OPT2_ "$@"
fi
}
_OPT2_() {
if [[ -z "${3:-}" ]]
then
shift
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
elif [[ "${3//-}" = [Ii]* ]]
then
shift 2
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTRO_ "$@"
elif [[ "${3//-}" = [Mm][Ii]* ]]
then
shift 2
printf "%s\\n" "Setting mode to manual install."
OPT=MANUAL
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
elif [[ "${3//-}" = [Mm]* ]]
then
shift 2
printf "%s\\n" "Setting mode to manual."
OPT=MANUAL
_OPT2_ "$@"
elif [[ "${3//-}" = [Rr][Ee][Ff][Rr][Ee]* ]]
then
shift 2
printf "\\nSetting mode to full refresh.\\n"
_PRPREFRESH_ "5"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${3//-}" = [Rr][Ee][Ff][Rr]* ]]
then
shift 2
printf "\\nSetting mode to 4 refresh.\\n"
_PRPREFRESH_ "4"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${3//-}" = [Rr][Ee][Ff]* ]]
then
shift 2
printf "\\nSetting mode to 3 refresh.\\n"
_PRPREFRESH_ "3"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${3//-}" = [Rr][Ee]* ]]
then
shift 2
printf "\\nSetting mode to 2 refresh.\\n"
_PRPREFRESH_ "2"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
elif [[ "${3//-}" = [Rr]* ]]
then
shift 2
printf "\\nSetting mode to 1 refresh.\\n"
_PRPREFRESH_ "1"
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
_INTROREFRESH_ "$@"
else
shift
_ARG2DIR_ "$@"
_PREPTERMUXARCH_
fi
}
_PREPTMPDIR_() {
[ -d "$INSTALLDIR/tmp" ] || { mkdir -p "$INSTALLDIR/tmp" && chmod 777 "$INSTALLDIR/tmp" && chmod +t "$INSTALLDIR/tmp" ; }
TAMPDIR="$INSTALLDIR/tmp/${0##*/}$STIME$PPID"
[ -d "$TAMPDIR" ]|| mkdir -p "$TAMPDIR"
}
_PREPTERMUXARCH_() {
_NAMEINSTALLDIR_
_NAMESTARTARCH_
_PREPTMPDIR_ || _PSGI1ESTRING_ "_PREPTMPDIR_ _PREPTERMUXARCH_ ${0##*/}"
_EDITORCHOOSER_
}
_PRINTERRORMSG_() {
printf "\\e[1;31m%s\\e[1;37m%s\\n\\n" "Signal generated in '$1'; Cannot complete task; " "Continuing..."
printf "\\e[1;34mIf you find improvements for \\e[0;34m'%s' \\e[1;34mplease open an issue and an accompanying pull request.  A pull request can assist in shedding more light on an issue.\\n\\n" "${0##*/}"
}
_PRPREFRESH_() {
printf "\\n%s\\n" "Refresh mode is set to refresh mode $1;  Initializing system refresh..."
LCR="$1"
}
_PRINTCONFLOADED_() {
printf "\\n\\e[0;34m%s \\e[1;34m%s \\e[0;32m%s\\e[1;32m%s \\e[1;34m%s \\e[1;32m%s\\n" " 🕛 > 🕑" "TermuxArch configuration" "$WDIR" "setupTermuxArchConfigs.bash" "loaded:" "OK"
}
_PRINTSHA512SYSCHKER_() {
printf "\\n\\e[07;1m\\e[31;1m\\n%s \\e[34;1m\\e[30;1m%s \\n\\e[0;0m\\n" " 🔆 ＴｅｒｍｕｘＡｒｃｈ ${PGNM^^} NOTICE sha512sum mismatch!  Setup initialization mismatch!  Is your wireless on?" "  Try again, initialization was not successful this time.  Wait a little while.  Then run the command 'bash $STRNRG' again..."
printf '\033]2; Run %s again...\007' "bash $STRNRG"
exit 124
}
_PRINTSTARTBIN_USAGE_() {
_NAMESTARTARCH_
if [[ -x "$(command -v "$STARTBIN")" ]]
then
printf "\\e[1;38;5;155m\\n%s\\n" "$STARTBIN help"
"$STARTBIN" help
printf "\\n"
else
printf "\\n"
fi
}
_PRINTUSAGE_() {
printf "\\n\\e[1;32m  %s     \\e[0;32mcommands \\e[1;32m%s \\e[0;32m%s\\n" "HELP" "'${0##*/} he[lp]'" "shows this help screen."
printf "\\n\\e[1;32m  %s    \\e[0;32mcommand \\e[1;32m%s \\e[0;32m%s\\n" "TERSE" "'${0##*/} he[lp]'" "shows the terse help screen."
printf "\\n\\e[1;32m  %s  \\e[0;32mcommand \\e[1;32m%s \\e[0;32m%s\\n" "VERBOSE" "'${0##*/} h[elp]'" "shows the verbose help screen."
printf "\\n\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\n" "Usage information for" " ${0##*/}" " version $VERSIONID.  Some arguments can be abbreviated to one, two and three letters each;  Two and three letter arguments are acceptable.  For example" " 'bash ${0##*/} cs'" " will use 'curl' to download TermuxArch and produce a file like" " setupTermuxArchSysInfo$STIME.log" " populated with system information.  If you have a new smartphone that you are not familiar with, once created, this file" " setupTermuxArchSysInfo$STIME.log" " might make for an interesting read in order to find out more about the device you might be holding in the palm of your hand right at this moment.  User configurable variables are in file" " setupTermuxArchConfigs.bash." "  To create this file from file" " knownconfigurations.bash" " in the working directory, execute" " 'bash ${0##*/} manual'" " to create and edit file" " setupTermuxArchConfigs.bash" "."
printf "\\n\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\e[1;32m%s\\e[0;32m%s\\n" "  INSTALL" "  You can run" " ${0##*/}" " without arguments in a bash shell to install Arch Linux in a PRoot QEMU container in a smartphone, smartTV, table, wearable and more...  Command" " 'bash ${0##*/} curl'"  " will envoke 'curl' as the download manager.  You can copy" " knownconfigurations.bash" " to" " setupTermuxArchConfigs.bash" " with the command" " 'bash ${0##*/} manual'" " - 👋 Hi, I’m @thannawat152005
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
thannawat152005/thannawat152005 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->