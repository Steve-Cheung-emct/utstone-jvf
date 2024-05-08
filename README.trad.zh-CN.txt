虛擬字體的通用設計

這一章講虛擬字體通用設計，主旨是在 ujlreq 和 utmin10 的基礎上，為簡體中文直排配置完全覆蓋 URO 和 BMP 漢字的標準虛擬字體。

綜述

總體設計思路：

一、面嚮 dvipdfmx 的 mapline／jfm

呼號説明：up-min-l-ja	四個字段，

字段 1：排版方向
字段 2：字形描述
字段 3：字重，最重要的參數之一
字段 4：自定義。字段 4 與廠家無關。

字段 1：
up	橫排；
ut	直排；

字段 2：
min	Mincho，明朝体；
go	Gothic，哥特体，這里活用做楷書、仿宋或者黑體
mg	MaruGothic，圓體
ks	kaisho，楷書字體
fs	fangsong，仿宋字體（宋朝体）


字段 3：
簡寫	名稱			W 字重法描述	說明
t/ul	UltraLight/Thin		W100		極細體（未使用）
j	ExtraLight		W200		超細體（未使用）
l	Light			W300		細體
r	Regular			W400		常規
m	Medium			W500		中等（未使用）
d	DemiBold/Semibold	W600		半粗體（未使用）
b	Bold			W700		粗體
e	ExtremeBold		W800		超粗體
h/ub	UltraBold/Heavy/Black	W900		極粗體（未使用）
（非常規）
lb	Light-Bold		W300		半細體，細體的橫筆加粗版，相當於 UD（未使用）
rb	Regular-Bold		W400		半常規，常規的橫筆加粗版，相當於 UD（未使用）


字段 4（自定義）：

ja	表示日文基幹
uro	當不映射思源時，盒子不會縮窄到 96％，此時使用 uro 作為呼號；
symt	表示映射給思源；注意：如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
cjka	CODESPACE 為 CJK-A
cjkb	CODESPACE 為 CJK-B
cjkc	CODESPACE 為 CJK-C
cjkd	CODESPACE 為 CJK-D（當物理字體僅覆蓋到 CJK-D 時可用此配置為 CJK-E、CJK-F 單獨指定物理字體）
cjke	CODESPACE 為 CJK-E
cjkf	CODESPACE 為 CJK-F
plane02	CODESPACE 為 02 平面
plane15	CODESPACE 為 15 平面
bd	標點（bd）：用作標點符號替換
hq	橫排時標點參照，用作蝌蚪引號轉置（重要）
fs	映射給仿宋
ks	映射給楷書
lxgw	映射給霞鶩文楷


二、map files

1、一般知識

字族名：tsuku

字族名即為文件夾之名稱。

安裝目錄：

C:\texlive\texmf-local\fonts\map\dvipdfmx

安裝方式：

整個文件夾直接拷貝過去，完成後使用管理員打開 CMD 執行 

mktexlsr

以及管理員執行（一共四條命令）

kanji-config-updmap-sys --jis2004 tsuku
kanji-config-updmap-sys --sc tsuku
kanji-config-updmap-sys --tc tsuku

文件説明：

otf-sc-tsuku.map	簡體中文 CID
otf-tc-tsuku.map	繁體中文 CID
uptex-sc-tsuku.map	簡體中文 URO
uptex-tc-tsuku.map	繁體中文 URO
otf-tsuku.map		日文 CID 並 UID（為 otf.sty 配置）
otf-up-tsuku.map	日文 URO（為 otf.sty 配置）
ptex-tsuku.map		日文 ISO-2022-JP（表外類推）
ptex-tsuku-04.map	日文 ISO-2022-JP（新字形）
uptex-tsuku.map		為 plain UpTeX 配置的 mapline
uptex-tsuku-04.map	為 upLaTeX 配置的 mapline，自定義設置全在這裏面

2、map files 的結構

jfm 呼號  映射編碼方式  實際字體  ［  轉置參數   可選參數  ］

jfm ：jvf 中配置虛擬盒子時所使用字體代號，因此稱之為「呼號」，僅僅只是一個代號，你也可以給它取個 「字母-9527」，儘量不要和系統上已有的 tfm（西文的 tfm）重名衝突。

編碼映射方式，常見的有三種：

第一種是 Adobe Ordering，亦即

Adobe Ordering		地區		映射編碼方式					opentype 的實現（舉例）
Adobe Japan1-3		日語		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		Heisei Mincho Std W9.otf
Adobe Japan1-4		日語		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		IwaSouPro-Md.otf
Adobe Japan1-5		日語		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		HiraMinProN-W3.otf
Adobe Japan1-6		日語		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		A-OTF-RyuminPr6-Light.otf
Adobe Japan1-6		日語		UniJIS2004-UTF16-H／V；UniJIS-UCS2-H／V		A-OTF-RyuminPr6N-Light.otf
Adobe GB1-4		簡體中文	UniGB-UTF16-H／V；UniGB-UCS2-H／V		AdobeSongStd-Light.otf
Adobe CNS1-3		繁體中文	UniCNS-UTF16-H／V；UniCNS-UCS2-H／V		AdobeFanHeitiStd-Bold.otf
Adobe-KR-2		朝鮮語／韓國語	UniKS-UTF16-H／V；UniKS-UCS2-H／V		MO-UDShinGoHangKo2-Reg.otf


除了 UTF16 還有 UTF32 也能映射，示例

\AtBeginDvi{
  \special{pdf:mapline r-up-min-r-ja   UniJISX02132004-UTF32-H  A-OTF-RyuminPr6N-Light.otf }%
}

能用，但沒必要。

Adobe Ordering 的字體比較特別，dvipdfmx 不需要讀字體的 cmap，只需要查 Adobe 的表就能完成字形的子集化嵌入。

第二種是 非 Adobe Ordering，哈哈，所有的 TTF 以及絕大多數國標 OTF 字體都屬於這類。

橫向 mapline 可以這樣寫

呼號	映射方式	物理字體
upbw-a    unicode    FZBWKSGBX.OTF

縱向的 mapline 要這樣寫

utbw-a    unicode    FZBWKSGBX.OTF  -w  1

説明，轉置參數適用於非 Adobe Ordering 的字體，參數為  「 -w 1 」，意思是將漢字或全寬字符旋轉 90°正置。

「jfm 呼號  映射編碼方式  實際字體  ［  轉置參數   可選參數  ］」

可選參數為 「 -v 10 」， 「-v 」 表示改變 stemV 値，値配置為 10；注意，在 Windows 系統上，數值配置的越小越黑，一般配置為 50／

第三種類型的，像是 Adobe 自己做的非規格字體，或者 思源字體，都有自己專用的映射鏈，視情況而定，有的僅有橫向映射鏈，縱向時還是只能使用 unicode／此處按下不表，應用面窄也不消舉例了。

3、mapfiles 的自定義修改

創建字族：

可將已有之字族複製到桌面再重命名，注意，目錄本身的名稱即為字族全名，然後在内部的每一個文件名上重複這個關鍵字。

例

拷貝 C:\texlive\2024\texmf-dist\fonts\map\dvipdfmx\ptex-fontmaps\kozuka-pr6n

至桌面，將 kozuka-pr6n 重命名為我的字族名 steve

然後將内部所有的文件名稱全部重命名：

ren    otf-kozuka-pr6n.map    otf-steve.map
ren    otf-up-kozuka-pr6n.map    otf-up-steve.map
ren    ptex-kozuka-pr6n-04.map    ptex-steve-04.map
ren    ptex-kozuka-pr6n.map    ptex-steve.map
ren    uptex-kozuka-pr6n-04.map    uptex-steve-04.map
ren    uptex-kozuka-pr6n.map    uptex-steve.map

為每個 jfm 單獨配置映射物理字體後，再將此文件目錄整個拷貝到 C:\texlive\texmf-local\fonts\map\dvipdfmx

管理員 cmd 執行 mktexlsr 以及

kanji-config-updmap-sys --jis2004 steve




三、CID 安裝

管理員打開 CMD，然後輸入

cd C:\texlive\2024\texmf-dist\scripts\cjk-gs-integrate

切換目錄到 cjk-gs-integrate 目錄下

先清理 CID 的舊緩存，執行

perl cjk-gs-integrate.pl --cleanup

再執行

perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local

1、如果出現錯誤，錯誤内容為「已存在 CIDFONT」（意譯），則根據提示找到 CIDFONT 所在的目錄，手動刪除，再次執行 「perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local」

在 README 中：

perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local

（ 在 TL2024 中，在一些情况下要加 --force 参数。因为部分字体文件在 C:/windows/Fonts 目录 和 TL 的字体目录中，存在相同 PS 名称，即使手动删除 CIDFONT 所在的目錄，也会存在 「已存在 CIDFONT」错误。）



2、如果出現錯誤，錯誤内容為 Perl 不是可執行的程序。

解決方法一：安裝 ActivePerl-5.28.1.2801-MSWin32-x64-24563874.exe

解決方法二：安裝 texlive2024 後，為了能够直接運行 perl 脚本有若干種方法。

把 texlive 自帶的 perl 加入設置系統環境變量：

加入方法是在 變量名：PATH 中增加

“C:\texlive\2024\tlpkg\tlperl\bin”

由于 texlive2024 把 perl 解釋器的版本升級為 perl5 (revision 5 version 38 subversion 2)，在中文版 windows 系統中使用會報錯：Locale 'Chinese (Simplified)_China.936' is unsupported, and may crash the interpreter.（繁體中文下為 cpc950）

原因是系統語言為中文（運行：systeminfo | findstr /B /C:"System Locale" 會顯示：System Locale: zh-cn;Chinese (China)，說明系統環境是中文環境）

為了避免這個錯誤，需要添加系統環境變量：

變量名：LANG 
變量值：zh_CN.UTF-8

（繁體的話，可類推 zh_TW.UTF-8(?)來個小白鼠驗證一下）

重啓後生效。

方法三： Strawberry Perl （http://strawberryperl.com/）

為了和 texlive 默認環境保持一致，推薦下載 Perl 5.38.2.x 版本。
然後，在 C:\texlive\2024\texmf.cnf 中，將 TEXLIVE_WINDOWS_TRY_EXTERNAL_PERL = 0 改為 TEXLIVE_WINDOWS_TRY_EXTERNAL_PERL = 1
以便啓用新安裝的 perl 解釋器。

四、NFSS 示例

```
% mincho light; 明朝体，細
\DeclareFontFamily{JY2}{utmc}{}
\DeclareFontFamily{JT2}{utmc}{}

\DeclareFontShape{JY2}{utmc}{l}{n}{<->s*[0.924690]upml-apr}{}
\DeclareFontShape{JY2}{utmc}{l}{it}{<->ssub*utmc/l/n}{}
\DeclareFontShape{JY2}{utmc}{l}{sl}{<->ssub*utmc/l/n}{}
\DeclareFontShape{JY2}{utmc}{l}{sc}{<->ssub*utmc/l/n}{}
\DeclareFontShape{JT2}{utmc}{l}{n}{<->s*[0.924690]utml-apr}{}
\DeclareFontShape{JT2}{utmc}{l}{it}{<->ssub*utmc/l/n}{}
\DeclareFontShape{JT2}{utmc}{l}{sl}{<->ssub*utmc/l/n}{}
\DeclareFontShape{JT2}{utmc}{l}{sc}{<->ssub*utmc/l/n}{}

\DeclareFontShape{JY2}{utmc}{m}{n}{<->s*[0.924690]upmr-apr}{}
\DeclareFontShape{JY2}{utmc}{m}{it}{<->ssub*utmc/m/n}{}
\DeclareFontShape{JY2}{utmc}{m}{sl}{<->ssub*utmc/m/n}{}
\DeclareFontShape{JY2}{utmc}{m}{sc}{<->ssub*utmc/m/n}{}
\DeclareFontShape{JT2}{utmc}{m}{n}{<->s*[0.924690]utmr-apr}{}
\DeclareFontShape{JT2}{utmc}{m}{it}{<->ssub*utmc/m/n}{}
\DeclareFontShape{JT2}{utmc}{m}{sl}{<->ssub*utmc/m/n}{}
\DeclareFontShape{JT2}{utmc}{m}{sc}{<->ssub*utmc/m/n}{}

\DeclareFontShape{JY2}{utmc}{bx}{n}{<->s*[0.924690]upmb-apr}{}
\DeclareFontShape{JY2}{utmc}{bx}{it}{<->ssub*utmc/bx/n}{}
\DeclareFontShape{JY2}{utmc}{bx}{sl}{<->ssub*utmc/bx/n}{}
\DeclareFontShape{JY2}{utmc}{bx}{sc}{<->ssub*utmc/bx/n}{}
\DeclareFontShape{JT2}{utmc}{bx}{n}{<->s*[0.924690]utmb-apr}{}
\DeclareFontShape{JT2}{utmc}{bx}{it}{<->ssub*utmc/bx/n}{}
\DeclareFontShape{JT2}{utmc}{bx}{sl}{<->ssub*utmc/bx/n}{}
\DeclareFontShape{JT2}{utmc}{bx}{sc}{<->ssub*utmc/bx/n}{}

% gothic regular; 黑体，常規
\DeclareFontFamily{JY2}{utgt}{}
\DeclareFontFamily{JT2}{utgt}{}

\DeclareFontShape{JY2}{utgt}{m}{n}{<->s*[0.924690]upgr-apr}{}
\DeclareFontShape{JY2}{utgt}{m}{it}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JY2}{utgt}{m}{sl}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JY2}{utgt}{m}{sc}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JY2}{utgt}{l}{n}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JT2}{utgt}{m}{n}{<->s*[0.924690]utgr-apr}{}
\DeclareFontShape{JT2}{utgt}{m}{it}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JT2}{utgt}{m}{sl}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JT2}{utgt}{m}{sc}{<->ssub*utgt/m/n}{}
\DeclareFontShape{JT2}{utgt}{l}{n}{<->ssub*utgt/m/n}{}

\DeclareFontShape{JY2}{utgt}{bx}{n}{<->s*[0.924690]upgb-apr}{}
\DeclareFontShape{JY2}{utgt}{bx}{it}{<->ssub*utgt/bx/n}{}
\DeclareFontShape{JY2}{utgt}{bx}{sl}{<->ssub*utgt/bx/n}{}
\DeclareFontShape{JY2}{utgt}{bx}{sc}{<->ssub*utgt/bx/n}{}
\DeclareFontShape{JT2}{utgt}{bx}{n}{<->s*[0.924690]utgb-apr}{}
\DeclareFontShape{JT2}{utgt}{bx}{it}{<->ssub*utgt/bx/n}{}
\DeclareFontShape{JT2}{utgt}{bx}{sl}{<->ssub*utgt/bx/n}{}
\DeclareFontShape{JT2}{utgt}{bx}{sc}{<->ssub*utgt/bx/n}{}

\DeclareFontShape{JY2}{utgt}{eb}{n}{<->s*[0.924690]upge-apr}{}
\DeclareFontShape{JY2}{utgt}{eb}{it}{<->ssub*utgt/eb/n}{}
\DeclareFontShape{JY2}{utgt}{eb}{sl}{<->ssub*utgt/eb/n}{}
\DeclareFontShape{JY2}{utgt}{eb}{sc}{<->ssub*utgt/eb/n}{}
\DeclareFontShape{JT2}{utgt}{eb}{n}{<->s*[0.924690]utge-apr}{}
\DeclareFontShape{JT2}{utgt}{eb}{it}{<->ssub*utgt/eb/n}{}
\DeclareFontShape{JT2}{utgt}{eb}{sl}{<->ssub*utgt/eb/n}{}
\DeclareFontShape{JT2}{utgt}{eb}{sc}{<->ssub*utgt/eb/n}{}

% maru gothic, only one weight
% 圓体，常規
\DeclareFontFamily{JY2}{mg}{}
\DeclareFontFamily{JT2}{mg}{}

\DeclareFontShape{JY2}{mg}{m}{n}{<->s*[0.924690]upmg-apr}{}
\DeclareFontShape{JY2}{mg}{m}{it}{<->ssub*mg/m/n}{}
\DeclareFontShape{JY2}{mg}{m}{sl}{<->ssub*mg/m/n}{}
\DeclareFontShape{JY2}{mg}{m}{sc}{<->ssub*mg/m/n}{}
\DeclareFontShape{JY2}{mg}{l}{n}{<->ssub*mg/m/n}{}
\DeclareFontShape{JY2}{mg}{bx}{n}{<->ssub*mg/m/n}{}
\DeclareFontShape{JY2}{mg}{b}{n}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{m}{n}{<->s*[0.924690]utmg-apr}{}
\DeclareFontShape{JT2}{mg}{m}{it}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{m}{sl}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{m}{sc}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{l}{n}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{bx}{n}{<->ssub*mg/m/n}{}
\DeclareFontShape{JT2}{mg}{b}{n}{<->ssub*mg/m/n}{}

\DeclareRobustCommand\ujmgrn{\kanjifamily{mg}\kanjiseries{m}\selectfont}

\renewcommand\mcdefault{utmc}
\renewcommand\gtdefault{utgt}

\renewcommand\kanjifamilydefault{\mcdefault}
\renewcommand\kanjiseriesdefault{\mddefault}

```

或者

```
% mincho light; 明朝体，細
\DeclareFontFamily{JY2}{ugmin}{}
\DeclareFontFamily{JT2}{ugmin}{}

\DeclareFontShape{JY2}{ugmin}{l}{n}{<->s*[0.961000]upgb-min-l}{}
\DeclareFontShape{JY2}{ugmin}{l}{it}{<->ssub*ugmin/l/n}{}
\DeclareFontShape{JY2}{ugmin}{l}{sl}{<->ssub*ugmin/l/n}{}
\DeclareFontShape{JY2}{ugmin}{l}{sc}{<->ssub*ugmin/l/n}{}
\DeclareFontShape{JT2}{ugmin}{l}{n}{<->s*[0.961000]utgb-min-l}{}
\DeclareFontShape{JT2}{ugmin}{l}{it}{<->ssub*ugmin/l/n}{}
\DeclareFontShape{JT2}{ugmin}{l}{sl}{<->ssub*ugmin/l/n}{}
\DeclareFontShape{JT2}{ugmin}{l}{sc}{<->ssub*ugmin/l/n}{}

\DeclareFontShape{JY2}{ugmin}{m}{n}{<->s*[0.961000]upgb-min-r}{}
\DeclareFontShape{JY2}{ugmin}{m}{it}{<->ssub*ugmin/m/n}{}
\DeclareFontShape{JY2}{ugmin}{m}{sl}{<->ssub*ugmin/m/n}{}
\DeclareFontShape{JY2}{ugmin}{m}{sc}{<->ssub*ugmin/m/n}{}
\DeclareFontShape{JT2}{ugmin}{m}{n}{<->s*[0.961000]utgb-min-r}{}
\DeclareFontShape{JT2}{ugmin}{m}{it}{<->ssub*ugmin/m/n}{}
\DeclareFontShape{JT2}{ugmin}{m}{sl}{<->ssub*ugmin/m/n}{}
\DeclareFontShape{JT2}{ugmin}{m}{sc}{<->ssub*ugmin/m/n}{}

\DeclareFontShape{JY2}{ugmin}{bx}{n}{<->s*[0.961000]upgb-min-b}{}
\DeclareFontShape{JY2}{ugmin}{bx}{it}{<->ssub*ugmin/bx/n}{}
\DeclareFontShape{JY2}{ugmin}{bx}{sl}{<->ssub*ugmin/bx/n}{}
\DeclareFontShape{JY2}{ugmin}{bx}{sc}{<->ssub*ugmin/bx/n}{}
\DeclareFontShape{JT2}{ugmin}{bx}{n}{<->s*[0.961000]utgb-min-b}{}
\DeclareFontShape{JT2}{ugmin}{bx}{it}{<->ssub*ugmin/bx/n}{}
\DeclareFontShape{JT2}{ugmin}{bx}{sl}{<->ssub*ugmin/bx/n}{}
\DeclareFontShape{JT2}{ugmin}{bx}{sc}{<->ssub*ugmin/bx/n}{}

% gothic regular; 黑体，常規
\DeclareFontFamily{JY2}{uggo}{}
\DeclareFontFamily{JT2}{uggo}{}

\DeclareFontShape{JY2}{uggo}{m}{n}{<->s*[0.961000]upgb-go-r}{}
\DeclareFontShape{JY2}{uggo}{m}{it}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JY2}{uggo}{m}{sl}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JY2}{uggo}{m}{sc}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JY2}{uggo}{l}{n}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JT2}{uggo}{m}{n}{<->s*[0.961000]utgb-go-r}{}
\DeclareFontShape{JT2}{uggo}{m}{it}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JT2}{uggo}{m}{sl}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JT2}{uggo}{m}{sc}{<->ssub*uggo/m/n}{}
\DeclareFontShape{JT2}{uggo}{l}{n}{<->ssub*uggo/m/n}{}

\DeclareFontShape{JY2}{uggo}{bx}{n}{<->s*[0.961000]upgb-go-b}{}
\DeclareFontShape{JY2}{uggo}{bx}{it}{<->ssub*uggo/bx/n}{}
\DeclareFontShape{JY2}{uggo}{bx}{sl}{<->ssub*uggo/bx/n}{}
\DeclareFontShape{JY2}{uggo}{bx}{sc}{<->ssub*uggo/bx/n}{}
\DeclareFontShape{JT2}{uggo}{bx}{n}{<->s*[0.961000]utgb-go-b}{}
\DeclareFontShape{JT2}{uggo}{bx}{it}{<->ssub*uggo/bx/n}{}
\DeclareFontShape{JT2}{uggo}{bx}{sl}{<->ssub*uggo/bx/n}{}
\DeclareFontShape{JT2}{uggo}{bx}{sc}{<->ssub*uggo/bx/n}{}

\DeclareFontShape{JY2}{uggo}{eb}{n}{<->s*[0.961000]upgb-go-e}{}
\DeclareFontShape{JY2}{uggo}{eb}{it}{<->ssub*uggo/eb/n}{}
\DeclareFontShape{JY2}{uggo}{eb}{sl}{<->ssub*uggo/eb/n}{}
\DeclareFontShape{JY2}{uggo}{eb}{sc}{<->ssub*uggo/eb/n}{}
\DeclareFontShape{JT2}{uggo}{eb}{n}{<->s*[0.961000]utgb-go-e}{}
\DeclareFontShape{JT2}{uggo}{eb}{it}{<->ssub*uggo/eb/n}{}
\DeclareFontShape{JT2}{uggo}{eb}{sl}{<->ssub*uggo/eb/n}{}
\DeclareFontShape{JT2}{uggo}{eb}{sc}{<->ssub*uggo/eb/n}{}

% maru gothic, only one weight
% 圓体，常規
\DeclareFontFamily{JY2}{ugmg}{}
\DeclareFontFamily{JT2}{ugmg}{}

\DeclareFontShape{JY2}{ugmg}{m}{n}{<->s*[0.961000]upgb-mg-r}{}
\DeclareFontShape{JY2}{ugmg}{m}{it}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JY2}{ugmg}{m}{sl}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JY2}{ugmg}{m}{sc}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JY2}{ugmg}{l}{n}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JY2}{ugmg}{bx}{n}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JY2}{ugmg}{b}{n}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{m}{n}{<->s*[0.961000]utgb-mg-r}{}
\DeclareFontShape{JT2}{ugmg}{m}{it}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{m}{sl}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{m}{sc}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{l}{n}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{bx}{n}{<->ssub*ugmg/m/n}{}
\DeclareFontShape{JT2}{ugmg}{b}{n}{<->ssub*ugmg/m/n}{}

\DeclareRobustCommand\ujugmgrn{\kanjifamily{ugmg}\kanjiseries{m}\selectfont}

\renewcommand\mcdefault{ugmin}
\renewcommand\gtdefault{uggo}

\renewcommand\kanjifamilydefault{\mcdefault}
\renewcommand\kanjiseriesdefault{\mddefault}


```

在 NFSS 中配置了 jvf，而 jfm 的映射鏈交由 map files 来處理，這裏不需要再配置 AtBeginDvi



五、面嚮 NFSS／jvf （這部分需要與下文對看）

總體規劃

utstone-jan	jlreq 橫排 100％ 寬幅標點符號，相鄰標點 150％ 寬幅；直排 100％ 寬幅，相鄰標點 150％ 寬幅；映射非思源字體，不擠壓漢字盒子
utstone-feb	jlreq 橫排 100％ 寬幅標點符號，相鄰標點 150％ 寬幅；直排 100％ 寬幅，相鄰標點 150％ 寬幅；映射思源字體，要將盒子橫向擠壓 96％；
utstone-mars	jlreq 橫排 75％ 寬幅標點符號，相鄰標點 125％ 寬幅；直排 50％ 寬幅，相鄰標點 100％ 寬幅；映射非思源字體，不擠壓漢字盒子
utstone-apr	jlreq 橫排 75％ 寬幅標點符號，相鄰標點 125％ 寬幅；直排 50％ 寬幅，相鄰標點 100％ 寬幅；映射思源字體，要將盒子橫向擠壓 96％；
utstone-vert	行間標點：jlreq 橫排 75％ 寬幅標點符號，相鄰標點 125％ 寬幅；映射思源字體，要將盒子橫向擠壓 96％；行間標點符號，轉置後著紅色。暫定轉置方案為：成對標點符號轉為 0 寬角引號。
utstone-qts	用作全唐詩的行間標點方案，成對標點符號不轉 0 寬，保留括弧，一律半寬。僅直排，單一字重。標點符號轉讀點，著紅色。
utstone-jun	割注字體：僅有直排：全寬標點符號，有 gluekern，相鄰標點 150％ 寬幅，明體風格 L／M 兩字重，割注字體。映射思源字體，要將盒子橫向擠壓 96％；
utstone-jul	單行小注字體：僅有直排：50％ 寬幅標點符號，相鄰標點 100％ 寬幅；明體風格 L／M 兩字重，仿宋、楷體風格各一字重，單行小字注釋。映射思源字體，要將盒子橫向擠壓 96％；




設計内容

一、以 ujlreq-v 為模板，直排時的通用設計

utml-apr
utmr-apr
utmb-apr
utmg-apr
utgr-apr
utgb-apr
utge-apr



utml-apr.zvp

序號	呼號（新）	說明
D0	ut-min-l-ja	D0 表示 0 區，默認漢字字體：u-Unicode，t 表示垂直，ut 表示垂直（up 表示橫排）；min 表示 mincho，明朝体；l-Light 字重；ja 表示默認為日語
D3	ut-min-l-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	ut-min-l-cjka	CJK-A；自定義
D5	ut-min-l-cjkb	CJK-B；ut 方正書局宋 L-02 平面
D6	ut-min-l-cjkc	CJK-C；ut 方正書局宋 L-02 平面
D7	ut-min-l-cjkd	CJK-D；ut 方正書局宋 L-02 平面（當物理字體僅覆蓋到 CJK-D 時可用此配置為 CJK-E、CJK-F 單獨指定物理字體）
D8	ut-min-l-cjke	CJK-E；ut 方正書局宋 L-02 平面
D9	ut-min-l-cjkf	CJK-F；ut 方正書局宋 L-02 平面
D10	ut-min-l-plane15	S-PUA:15 區漢字;ut 方正書局宋 L-15 平面
D11	ut-min-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-min-l-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjml-v	c 表示 cid，j 表示 Japanese，m 表示明朝，l-Light 字重（映射必須與 D0 完全一致）此字體用作内部映射，不參與外部映射。


utmr-apr.zvp

D0	ut-min-r-ja	D0 表示 0 區，默認漢字字體：u-Unicode，t 表示垂直，ut 表示垂直（up 表示橫排）；min 表示 mincho，明朝体；r-Regular 字重；ja 表示默認為日語
D3	ut-min-r-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	ut-min-r-cjka	CJK-A；自定義
D5	ut-min-r-cjkb	CJK-B；ut 方正書局宋 L-02 平面
D6	ut-min-r-cjkc	CJK-C；ut 方正書局宋 L-02 平面
D7	ut-min-r-cjkd	CJK-D；ut 方正書局宋 L-02 平面
D8	ut-min-r-cjke	CJK-E；ut 方正書局宋 L-02 平面
D9	ut-min-r-cjkf	CJK-F；ut 方正書局宋 L-02 平面
D10	ut-min-r-plane15	S-PUA:15 區漢字;ut 方正書局宋 L-15 平面
D11	ut-min-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-min-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmr-v	c 表示 cid，j 表示 Japanese，m 表示明朝，r-Regular 字重（映射必須與 D0 完全一致）此字體用作内部映射，不參與外部映射。

utmb-apr.zvp

D0	ut-min-b-ja	D0 表示 0 區，默認漢字字體：u-Unicode，t 表示垂直，ut 表示垂直（up 表示橫排）；min 表示 mincho，明朝体；b-Bold 字重；ja 表示默認為日語
D3	ut-min-b-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	ut-min-b-cjka	CJK-A；自定義
D5	ut-min-b-cjkb	CJK-B；ut 方正書局宋 L-02 平面
D6	ut-min-b-cjkc	CJK-C；ut 方正書局宋 L-02 平面
D7	ut-min-b-cjkd	CJK-D；ut 方正書局宋 L-02 平面
D8	ut-min-b-cjke	CJK-E；ut 方正書局宋 L-02 平面
D9	ut-min-b-cjkf	CJK-F；ut 方正書局宋 L-02 平面
D10	ut-min-b-plane15	S-PUA:15 區漢字;ut 方正書局宋 L-15 平面
D11	ut-min-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-min-b-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmb-v	c 表示 cid，j 表示 Japanese，m 表示明朝，b-Bold 字重（映射必須與 D0 完全一致）


utmg-apr.zvp

D0	ut-mg-r-ja	D0 表示 0 區，默認漢字字體：ut 表示垂直，mg 表示 MaruGothic 圓體，r-Regular 字重；ja 表示默認為日語
D3	ut-mg-r-rd	D3 表示 D0 在 BMP 上漢字的補集：rd 圓體的意思；
D4	ut-mg-r-cjka	CJK-A；自定義
D5	ut-mg-r-plane02		02 平面；ut 方正書局宋 L-02 平面（僅用作 fallback）
D11	ut-mg-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-mg-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmg-v	c 表示 cid，j 表示 Japanese，mg 表示 MaruGothic 圓體，r-Regular 字重，（映射必須與 D0 完全一致）


utgr-apr.zvp

D0	ut-go-r-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：r-Regular 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D3	ut-go-r-ks	D3 表示 D0 在 BMP 上漢字的補集：映射為 DF 仿宋 GBA Std W3；fs-表示仿宋
D4	ut-go-r-cjka	CJK-A；自定義
D5	ut-go-r-cjkb	CJK-B；漢儀仿宋 CJK-B(HYFangSong_GB18030Super-ExtB)
D6	ut-go-r-cjkc	CJK-C；ut 方正書局宋 L-02 平面
D7	ut-go-r-cjkd	CJK-D；ut 方正書局宋 L-02 平面（當物理字體僅覆蓋到 CJK-D 時可用此配置為 CJK-E、CJK-F 單獨指定物理字體）
D8	ut-go-r-cjke	CJK-E；ut 方正書局宋 L-02 平面
D9	ut-go-r-cjkf	CJK-F；ut 方正書局宋 L-02 平面
D10	ut-go-r-plane15	S-PUA:15 區漢字;ut 方正書局宋 L-15 平面（如果這里不配置，則需手動切換到明體 R。因為沒有替代，所以讓方正書局宋 15 平面充當臨時演員）
D11	ut-go-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-go-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjgr-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，r-Regular 字重（映射必須與 D0 完全一致）


utgb-apr.zvp

D0	t-go-b-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：b-Bold 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D3	t-go-b-ks	D3 表示 D0 在 BMP 上漢字的補集：映射為 DF 楷書 GBA Std W5；ks-表示楷書
D4	t-go-b-cjka	CJK-A；自定義
D5	t-go-b-cjkb	CJK-B；方正楷體 S-SIP：FZKaiS(SIP).TTF
D6	t-go-b-cjkc	CJK-C；ut 方正書局宋 L-02 平面
D7	t-go-b-cjkd	CJK-D；ut 方正書局宋 L-02 平面（當物理字體僅覆蓋到 CJK-D 時可用此配置為 CJK-E、CJK-F 單獨指定物理字體）
D8	t-go-b-cjke	CJK-E；ut 方正書局宋 L-02 平面
D9	t-go-b-cjkf	CJK-F；ut 方正書局宋 L-02 平面
D10	ut-go-b-plane15	S-PUA:15 區漢字;ut 方正書局宋 L-15 平面（如果這里不配置，則需手動切換到明體 R。因為沒有替代，所以讓方正書局宋 15 平面充當臨時演員）
D11	t-go-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	t-go-b-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	tf-cjgb-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，b-Bold 字重（映射必須與 D0 完全一致）


utge-apr.zvp

D0	ut-go-e-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：e-ExtremeBold 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D3	ut-go-e-ks	D3 表示 D0 在 BMP 上漢字的補集；粗體楷書字體
D4	ut-go-e-cjka	CJK-A；自定義
D5	ut-go-e-plane02		02 平面；ut 方正書局宋 L-02 平面（僅用作 fallback）
D11	ut-go-e-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	ut-go-e-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjge-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，ExtraBold 字重（映射必須與 D0 完全一致）


二、以 ujlreq 為模板，橫排時的通用設計

upml-apr
upmr-apr
upmb-apr
upmg-apr
upgr-apr
upgb-apr
upge-apr


upml-apr.zvp

序號	呼號（新）	說明
D0	up-min-l-ja	D0 表示 0 區，默認漢字字體：u-Unicode，up 表示橫排；min 表示 mincho，明朝体；l-Light 字重；ja 表示默認為日語
D2	up-min-l-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-min-l-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	up-min-l-cjka	CJK-A；自定義；如果映射的 uro 字體是 GBK 時，設置本項目為 CJK-A 的替字。如果映射的 uro 字體是 GB18030 的，將本項目設置為與 uro(symt)相同。
D5	up-min-l-cjkb	CJK-B；up 方正書局宋 L-02 平面
D6	up-min-l-cjkc	CJK-C；up 方正書局宋 L-02 平面
D7	up-min-l-cjkd	CJK-D；up 方正書局宋 L-02 平面
D8	up-min-l-cjke	CJK-E；up 方正書局宋 L-02 平面
D9	up-min-l-cjkf	CJK-F；up 方正書局宋 L-02 平面
D10	up-min-l-plane15	S-PUA:15 區漢字;up 方正書局宋 L-15 平面
D11	up-min-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-min-l-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjml-h	c 表示 cid，j 表示 Japanese，m 表示明朝，l-Light 字重（映射必須與 D0 完全一致）此字體用作内部映射，不參與外部映射。


upmr-apr.zvp

D0	up-min-r-ja	D0 表示 0 區，默認漢字字體：u-Unicode，up 表示橫排；min 表示 mincho，明朝体；r-Regular 字重；ja 表示默認為日語
D2	up-min-r-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-min-r-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	up-min-r-cjka	CJK-A；自定義
D5	up-min-r-cjkb	CJK-B；up 方正書局宋 L-02 平面
D6	up-min-r-cjkc	CJK-C；up 方正書局宋 L-02 平面
D7	up-min-r-cjkd	CJK-D；up 方正書局宋 L-02 平面
D8	up-min-r-cjke	CJK-E；up 方正書局宋 L-02 平面
D9	up-min-r-cjkf	CJK-F；up 方正書局宋 L-02 平面
D10	up-min-r-plane15	S-PUA:15 區漢字;up 方正書局宋 L-15 平面
D11	up-min-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-min-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmr-h	c 表示 cid，j 表示 Japanese，m 表示明朝，r-Regular 字重（映射必須與 D0 完全一致）此字體用作内部映射，不參與外部映射。

upmb-apr.zvp

D0	up-min-b-ja	D0 表示 0 區，默認漢字字體：u-Unicode，up 表示橫排；min 表示 mincho，明朝体；b-Bold 字重；ja 表示默認為日語
D2	up-min-b-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-min-b-symt	D3 表示 D0 在 BMP 上漢字的補集：思源明朝；同前；如果映射思源字體，要將盒子橫向擠壓 96％；如果映射的不是思源，則不需要擠壓，這裏產生分歧
D4	up-min-b-cjka	CJK-A；自定義
D5	up-min-b-cjkb	CJK-B；up 方正書局宋 L-02 平面
D6	up-min-b-cjkc	CJK-C；up 方正書局宋 L-02 平面
D7	up-min-b-cjkd	CJK-D；up 方正書局宋 L-02 平面
D8	up-min-b-cjke	CJK-E；up 方正書局宋 L-02 平面
D9	up-min-b-cjkf	CJK-F；up 方正書局宋 L-02 平面
D10	up-min-b-plane15	S-PUA:15 區漢字;up 方正書局宋 L-15 平面
D11	up-min-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-min-b-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmb-h	c 表示 cid，j 表示 Japanese，m 表示明朝，b-Bold 字重（映射必須與 D0 完全一致）


upmg-apr.zvp

D0	up-mg-r-ja	D0 表示 0 區，默認漢字字體：up 表示垂直，mg 表示 MaruGothic 圓體，r-Regular 字重；ja 表示默認為日語
D2	up-mg-r-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-mg-r-rd	D3 表示 D0 在 BMP 上漢字的補集：rd 圓體的意思；
D4	up-mg-r-cjka	CJK-A；自定義
D5	up-mg-r-plane02		02 平面；up 方正書局宋 L-02 平面（僅用作 fallback）
D11	up-mg-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-mg-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjmg-h	c 表示 cid，j 表示 Japanese，mg 表示 MaruGothic 圓體，r-Regular 字重，（映射必須與 D0 完全一致）


upgr-apr.zvp

D0	up-go-r-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：r-Regular 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D2	up-go-r-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-go-r-ks	D3 表示 D0 在 BMP 上漢字的補集：映射為 DF 仿宋 GBA Std W3；fs-表示仿宋
D4	up-go-r-cjka	CJK-A；自定義
D5	up-go-r-cjkb	CJK-B；漢儀仿宋 CJK-B(HYFangSong_GB18030Super-ExtB)
D6	up-go-r-cjkc	CJK-C；up 方正書局宋 L-02 平面
D7	up-go-r-cjkd	CJK-D；up 方正書局宋 L-02 平面
D8	up-go-r-cjke	CJK-E；up 方正書局宋 L-02 平面
D9	up-go-r-cjkf	CJK-F；up 方正書局宋 L-02 平面
D10	up-go-r-plane15	S-PUA:15 區漢字;up 方正書局宋 L-15 平面（如果這里不配置，則需手動切換到明體 R。因為沒有替代，所以讓方正書局宋 15 平面充當臨時演員）
D11	up-go-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-go-r-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjgr-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，r-Regular 字重（映射必須與 D0 完全一致）


upgb-apr.zvp

D0	up-go-b-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：b-Bold 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D2	up-go-b-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-go-b-ks	D3 表示 D0 在 BMP 上漢字的補集：映射為 DF 楷書 GBA Std W5；ks-表示楷書
D4	up-go-b-cjka	CJK-A；自定義
D5	up-go-b-cjkb	CJK-B；方正楷體 S-SIP：FZKaiS(SIP).TTF
D6	up-go-b-cjkc	CJK-C；up 方正書局宋 L-02 平面
D7	up-go-b-cjkd	CJK-D；up 方正書局宋 L-02 平面
D8	up-go-b-cjke	CJK-E；up 方正書局宋 L-02 平面
D9	up-go-b-cjkf	CJK-F；up 方正書局宋 L-02 平面
D10	up-go-b-plane15	S-PUA:15 區漢字;up 方正書局宋 L-15 平面（如果這里不配置，則需手動切換到明體 R。因為沒有替代，所以讓方正書局宋 15 平面充當臨時演員）
D11	up-go-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-go-b-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjgb-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，b-Bold 字重（映射必須與 D0 完全一致）


upge-apr.zvp

D0	up-go-e-ja	D0 表示 0 區，默認漢字字體：go-Gothic 黑體，黑體字族的呼號：e-ExtremeBold 字重；u 表示 Unicode，t 表示直排；ja 表示默認為日語
D2	up-go-e-hq	標點符號字體，用於在半寬寬度下輸出全寬蝌蚪引號。
D3	up-go-e-ks	D3 表示 D0 在 BMP 上漢字的補集；粗體楷書字體
D4	up-go-e-cjka	CJK-A；自定義
D5	up-go-e-plane02		02 平面；up 方正書局宋 L-02 平面（僅用作 fallback）
D11	up-go-e-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D12	up-go-e-bd	標點符號替換字體：把 D0 的標點符號更換為 D10，bd-標點的意思
D19	otf-cjge-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，ExtraBold 字重（映射必須與 D0 完全一致）


三、以 utmin10 為模板，直排時的通用設計

設計思路：此字體是 utmin10/umin10 的簡化版，且不考慮 Plane02 生僻字。專門用於支持 URO 的簡體漢字。適合單獨配置為 GBK/GB18030-2000 規格的簡體中文字體。繁體中文、日文不適用。

utgb-min-l

D0	r-ut-min-l-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-min-l-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-ut-min-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

utgb-min-r

D0	r-ut-min-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-min-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-ut-min-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjmr-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

utgb-min-b

D0	r-ut-min-b-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-min-b-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-ut-min-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjmb-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）


utgb-mg-r

D0	r-ut-mg-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-mg-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmg-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

utgb-go-r

D0	r-ut-go-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-go-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmr-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

utgb-go-b

D0	r-ut-go-b-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-go-b-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmb-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

utgb-go-e

D0	r-ut-go-e-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-go-e-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjme-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）



utgb-fs-l.zvp

窄體 86％（長仿宋）

D0	r-ut-min-l-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-fs-l-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D4	r-ut-fs-l-plane02	用作映射 plane02 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-ut-fs-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）



utgb-ks-l.zvp

正度楷體，100％ 寬度不縮放，CJK-B

D0	r-ut-min-l-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D3	r-ut-ks-l-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D4	r-ut-ks-l-plane02	用作映射 plane02 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-ut-ks-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）





四、以 umin10 為模板，橫排時的通用設計


upgb-min-l

D0	r-up-min-l-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-min-l-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-min-l-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-up-min-l-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjml-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

upgb-min-r

D0	r-up-min-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-min-r-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-min-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-up-min-r-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjmr-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

upgb-min-b

D0	r-up-min-b-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-min-b-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-min-b-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D11	r-up-min-b-lxgw	霞鶩文楷 L：用作伏羲六十四卦
D19	otf-cjmb-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）


upgb-mg-r

D0	r-up-mg-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-mg-r-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-mg-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmg-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

upgb-go-r

D0	r-up-go-r-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-go-r-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-go-r-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmr-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

upgb-go-b

D0	r-up-go-b-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-go-b-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-go-b-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjmb-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）

upgb-go-e

D0	r-up-go-e-ja	日文基幹，字體的主體，用作標點和雜項符號的映射
D2	r-up-go-e-hq	用作標點符號，半寬寬度下輸出全寬蝌蚪引號。
D3	r-up-go-e-uro	用作映射 URO 漢字，以 CTRANGE 指定漢字的範圍。
D19	otf-cjme-h	日文 CID，用作標點符號的轉置。（映射必須與 D0 完全一致）


五、垂直行間標點符號

1、以 ujlreq-v 為模板，不輸出成對標點符號（即前 x 號、後 x 號），標點轉讀點，著紅色

呼號與 utmr 系列相同，唯標點部分有差。

改訂方案：成對標點符號均被改為 Adobe Japan1-4 中的 cid12156 和 cid12157 。

min-l 和 min-r 兩個，都做了 96％ 96％ 的縮放，以實現在不改變模板的前提下拉伸字距。



著色辦法（顔色堆棧）：

(CHARACTER H 3002
   (MAP
      (SPECIAL color push cmyk 0 0.85 0.85 0)
      (MOVEUP R 0.22)
      (MOVERIGHT R -0.35)
      (SETCHAR H 3002)
      (SPECIAL color pop)
      )
   )


2、以 ujlreq-v 為模板，不輸出成對標點符號（即前 x 號、後 x 號），標點轉讀點，不著色

呼號與 utmr 系列相同，唯標點部分有差。鴿了。此方案已被廢棄。


3、以 ujlreq-v 為模板，輸出成對標點符號（嵌入行中，與豎排無異），行間標點不著色

呼號與 utmr 系列相同，唯標點部分有差。此方案已改為 4 中的（1）全唐詩注釋。

4、全唐詩注釋和全唐詩小字注釋

（1）全唐詩注釋呼號與 utmin10 相同，且增加生僻字處理。

改訂方案：成對標點符號均被改為 Adobe Japan1-4 中的 cid12156 和 cid12157 。額外的，由於全唐詩使用校勘符號（〔〕（））以及書名號（《》〈〉）不能被批量替為角引號，所以做了插入行間的處理。

（2）全唐詩小字注釋與 utmin10 相同，且增加生僻字處理。另外附加向右側的偏移向量

5、全角割注字體

呼號與 utmr 系列相同，唯 GLUEKERN 部分有差


6、單行小注字體

呼號與 utmr 系列相同，唯 GLUEKERN 部分有差；另外附加向右側的偏移向量

7、霞鶩文楷虛擬字體

呼號與 utmin10 系列相同，D0 部分替換為 Klee-Pro，且包含生僻字和康熙字典体。此方案已修改。

改為直接使用 URO 的模板。唯標點符號仍用日文 Klee-Pro／



六、全寬標點符號虛擬字體（橫排並直排）



七、橫排 80％ 寬幅擠壓的非標虛擬字體（irregular）

直排只做兩種，一種 50％ 寬幅，一種 100％ 寬幅。此方案已修改。

橫排只做 75％ 和全寬兩種。先做 utstone-apr，其餘全部依次複製。

八、已知存在的問題


1、0x00B7

在簡體中文排版中使用 0x00B7 作為中間間斷點代替 0x30FB ，在 word 排版的中文文本中沒有問題，但是！！但是，在 LaTeX 下是災難性的。

這將導致中文文本中所有 0x00B7 都變成變寬字符。起初沒有任何改良辦法，只能手動把 0x00B7 替換成 0x30FB 當然我也推薦這樣使用。

《詩・雅・鹿鳴》《文選・呉都賦》……

改良方案，在 jvf 中單獨為 0x00B7 設置了 標點符號 Type 表，ID 為 15

(CHARSINTYPE D 15
   U00B7
   )

橫排方案：字寬設為 0.333（1/3 寬字符 cid09778→0x2632）

(TYPE D 15
   (CHARWD R 0.333)
   (CHARHT R 0.88)
   (CHARDP R 0.12)
   (MAP
      (SELECTFONT D 19)
      (SETCHAR H 2632)
      )
   )

竪排方案：字寬設為 0.333（1/3 寬字符 cid13294→0x33EE）

(TYPE D 15
   (CHARWD R 0.333)
   (CHARHT R 0.5)
   (CHARDP R 0.5)
   (MAP
      (SELECTFONT D 19)
      (SETCHAR H 33EE)
      )
   )



2、竪排時使用割注虛擬字體 utstone-jun 會優先調用 JY2 而不是 JT2 


改了割注虛擬字體的配置，於是割注產生了變化。另外 TexLive2024 在竪排時，如果有 JY2 字體，可能會優先使用 JY2 ，導致豎排割注的 gluekern 失效！！如果沒有 JY2 才會使用 JT2 需要留意！！！

這樣的話，當局部切換到橫排時會缺失橫排字體，如果其中有割注將產生致命錯誤！當然，沒有誰會用橫排做割注吧？如果有，到時候需要用 \ifydir \iftdir 臨時切替一下，示例如下：

%%%%%%%%%%%%%%%%%%%%%%%% 筑 紫 明 朝 %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\ifydir
\DeclareFontFamily{JY2}{tsku}{}
\DeclareFontShape{JY2}{tsku}{m}{n}{<->s*[0.924690]upmr-jun}{}
\DeclareFontShape{JY2}{tsku}{m}{it}{<->ssub*tsku/m/n}{}
\DeclareFontShape{JY2}{tsku}{m}{sl}{<->ssub*tsku/m/n}{}
\DeclareFontShape{JY2}{tsku}{m}{sc}{<->ssub*tsku/m/n}{}
\fi

\DeclareFontFamily{JT2}{tsku}{}
\DeclareFontShape{JT2}{tsku}{m}{n}{<->s*[0.924690]utmr-jun}{}
\DeclareFontShape{JT2}{tsku}{m}{it}{<->ssub*tsku/m/n}{}
\DeclareFontShape{JT2}{tsku}{m}{sl}{<->ssub*tsku/m/n}{}
\DeclareFontShape{JT2}{tsku}{m}{sc}{<->ssub*tsku/m/n}{}

\DeclareRobustCommand\tsku{\kanjifamily{tsku}\kanjiseries{m}\selectfont}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%% 筑 紫 明 朝 %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


3、自定義字體（造字）怎麽加入虛擬字體？

使用 afdko 的 python 小程序 fonttools 取出造字的 cmap，

ttx -t cmap foo.ttf

然後提取漢字的 UID（ glyph name 是不必要的）


      <map code="0xe001" name="uE001"/><!-- ???? -->
      <map code="0xe002" name="uE002"/><!-- ???? -->
      <map code="0xe003" name="uE003"/><!-- ???? -->
      <map code="0xe004" name="uE004"/><!-- ???? -->
      <map code="0x2165f" name="u2165F"/><!-- CJK UNIFIED IDEOGRAPH-2165F -->
      <map code="0x23617" name="u23617"/><!-- CJK UNIFIED IDEOGRAPH-23617 -->
      <map code="0x25be8" name="u25BE8"/><!-- CJK UNIFIED IDEOGRAPH-25BE8 -->
      <map code="0x25db4" name="u25DB4"/><!-- CJK UNIFIED IDEOGRAPH-25DB4 -->
      <map code="0x27114" name="u27114"/><!-- CJK UNIFIED IDEOGRAPH-27114 -->
      <map code="0x27452" name="u27452"/><!-- CJK UNIFIED IDEOGRAPH-27452 -->
      <map code="0x274af" name="u274AF"/><!-- CJK UNIFIED IDEOGRAPH-274AF -->
      <map code="0x2771f" name="u2771F"/><!-- CJK UNIFIED IDEOGRAPH-2771F -->
      <map code="0x2783a" name="u2783A"/><!-- CJK UNIFIED IDEOGRAPH-2783A -->
      <map code="0x27cfd" name="u27CFD"/><!-- CJK UNIFIED IDEOGRAPH-27CFD -->
      <map code="0x29859" name="u29859"/><!-- CJK UNIFIED IDEOGRAPH-29859 -->
      <map code="0x2985f" name="u2985F"/><!-- CJK UNIFIED IDEOGRAPH-2985F -->
      <map code="0x29cb2" name="u29CB2"/><!-- CJK UNIFIED IDEOGRAPH-29CB2 -->
      <map code="0xf0b23" name="uE001"/><!-- ???? -->

將以上代碼格式化為

0xE001	
0xE002	
0xE003	
0xE004	
0x2165F	
0x23617	
0x25BE8	
0x25DB4	
0x27114	
0x27452	
0x274AF	
0x2771F	
0x2783A	
0x27CFD	
0x29859	
0x2985F	
0x29CB2	
0xF0B23	

再改為

UE001 UE002 UE003 UE004 U2165F U23617 U25BE8 U25DB4 U27114 U27452 U274AF U2771F U2783A U27CFD U29859 U2985F U29CB2 UF0B23 



在示例文件 myfont-m-jt2.zvp 和 myfont-m-jy2.zvp 中對自造漢字進行了簡短的定義。

找到 Source 裏面的 myfont-utmin10  創建 jvf 


perl  jfmutil.pl  zvp2vf -u   --lenient  myfont-m-jt2
perl  jfmutil.pl  zvp2vf -u   --lenient  myfont-m-jy2

找到 Source 裏面的 jfm-copy 複製 jfm

copy  urml.tfm    r-myfont-m-jy2.tfm
copy  urmlv.tfm   r-myfont-m-jt2.tfm

安裝 tfm

r-myfont-m-jy2.tfm  →  C:\texlive\texmf-local\fonts\tfm\［自定義創建一個 jfm 目錄］
r-myfont-m-jt2.tfm  →  C:\texlive\texmf-local\fonts\tfm\［自定義創建一個 jfm 目錄］

安裝 jvf

jfm：

myfont-m-jy2.tfm  →  C:\texlive\texmf-local\fonts\tfm\myfont
myfont-m-jt2.tfm  →  C:\texlive\texmf-local\fonts\tfm\myfont

jvf：

myfont-m-jy2.vf  →  C:\texlive\texmf-local\fonts\vf\myfont
myfont-m-jt2.vf  →  C:\texlive\texmf-local\fonts\vf\myfont

複製完，使用管理員打開 CMD 執行 mktexlsr

NFSS 配置：

%%% myfont ファミリーに m と db のシリーズを定義
\DeclareFontFamily{JY2}{myfont}{}
\DeclareFontFamily{JT2}{myfont}{}
\DeclareFontShape{JY2}{myfont}{m}{n}{<->s*[0.961000]myfont-m-jy2}{}
\DeclareFontShape{JY2}{myfont}{m}{it}{<->ssub*myfont/m/n}{}
\DeclareFontShape{JY2}{myfont}{m}{sl}{<->ssub*myfont/m/n}{}
\DeclareFontShape{JY2}{myfont}{m}{sc}{<->ssub*myfont/m/n}{}
\DeclareFontShape{JT2}{myfont}{m}{n}{<->s*[0.961000]myfont-m-jt2}{}
\DeclareFontShape{JT2}{myfont}{m}{it}{<->ssub*myfont/m/n}{}
\DeclareFontShape{JT2}{myfont}{m}{sl}{<->ssub*myfont/m/n}{}
\DeclareFontShape{JT2}{myfont}{m}{sc}{<->ssub*myfont/m/n}{}

\DeclareRobustCommand\myfont{\kanjifamily{myfont}\kanjiseries{m}\selectfont}

\AtBeginDvi{%%
\special{pdf:mapline r-myfont-m-jy2  unicode    myfont.ttf      }%
\special{pdf:mapline r-myfont-m-jt2  unicode    myfont.ttf -w 1 }%
}

在 LaTeX 中使用它需要 pxbase 包（\usepackage{pxbase}）支持使用 \UI{XXXX} 取出漢字：


正文正文正文{\myfont\UI{2165F}}正文正文正文


4、怎麽切換 NFSS 的配置，比方説，我想把紅樓夢模板的虛擬字體切換成 jlreq 的配置

切換方法簡單粗暴，直接批量替換

試查找：「-apr」

替換為：「-feb」

注意總體設計裏頭的幾種虛擬字體方案，當你配置 NFSS 時，一般只選取其中一種：用思源或者不用思源，橫排擠壓方案用 75％還是 100％，直排擠壓方案用 50％還是 100％

可以直接改 NFSS 引用 jvf 的後綴来達到切換配置文件的目的。


utstone-jan	jlreq 橫排 100％ 寬幅標點符號，相鄰標點 150％ 寬幅；直排 100％ 寬幅，相鄰標點 150％ 寬幅；映射非思源字體，不擠壓漢字盒子
utstone-feb	jlreq 橫排 100％ 寬幅標點符號，相鄰標點 150％ 寬幅；直排 100％ 寬幅，相鄰標點 150％ 寬幅；映射思源字體，要將盒子橫向擠壓 96％；
utstone-mars	jlreq 橫排 75％ 寬幅標點符號，相鄰標點 125％ 寬幅；直排 50％ 寬幅，相鄰標點 100％ 寬幅；映射非思源字體，不擠壓漢字盒子
utstone-apr	jlreq 橫排 75％ 寬幅標點符號，相鄰標點 125％ 寬幅；直排 50％ 寬幅，相鄰標點 100％ 寬幅；映射思源字體，要將盒子橫向擠壓 96％；




5、我安裝了 map_files、JFM、JVF，也執行了 mktexlsr ，也執行了 kanji-config-updmap-sys --jis2004 tsuku （kanji-config-updmap-sys --sc tsuku 以及 kanji-config-updmap-sys --sc tsuku）等的全部四條命令，也按要求配置了 cjk-gs-integrate，所有配置時都 no error 但為什麽無法輸出 PDF：提示找不到 真實物理字體：

說明：此虛擬字體配置方案的 map 分兩種，一種是 Windows 下的，一種是 OSX 的，沒有為 Linux 設計虛擬字體。

本配置方案不提供任何物理字體。 mapline 中使用的商業字體，在首次安裝時，必須被替換為客戶機上的客戶字體。即是，用戶必須嘗試為 uplatex 配置為本地字體。畢竟配置文件只是配置文件嘛。

配置方法，查找替換。

試查找：「KaitiSC-Black.ttf」

替換為：「STKAITI.TTF」

簡單粗暴，也很實用。


特別需要注意的是：

（1）所有的標點符號字體（-bd／biaodian／punctuations／約物）都是使用了日文 Pro（Adobe Japan 1-4／15444cid）及以上的日文字體，替換的時候不要替換成 Std／StdN（Adobe Japan 1-3／9354cid）的日文字體，標點符號會丟失。

（2）所有的日文明體字體（-ja／mincho／明朝体）都是按日文 Pr6／Pr6N（Adobe Japan 1-6／23058cid）配置的，替換過後的字體也必須是 Pr6／Pr6N，否則會丟失漢字。

（3）所有的日文哥特字體（-go／gothic／黑体）都是按日文 Pr5（Adobe Japan 1-5／20316cid）配置的，替換過後的字體也必須是 Pr5／Pr5N，否則會丟失漢字。

（4）所有的 uro 字體（-uro／Unicode）都是按 URO［(CTRANGE H 4E00 H 9FFF)］配置的，其中，0x9fa5（龥，不含）之後 0x9fa6（龦）到 0x9fff（鿿）的漢字，在 [_GBK] 後綴或者 [_GB18030] 後綴的字體中一般都不會配置，在 LaTeX 中輸出失敗也不是虛擬字體的問題，是物理字體本身没那個字。解決辦法：使用思源字體。但是思源也沒有 URO 末端最後的幾個字。

（5）所有的 Plane02 字體，都按 CJK 分區做了拆分，以便客戶單獨配置每個分區所映射的物理字體。

（6）垂直行間標點虛擬字體（utstone-vert）不能正確輸出校勘符號（〔〕（））以及書名號（《》〈〉），會直接轉為角引號。當使用校勘符號時，不要使用垂直行間標點虛擬字體！！！可以使用全唐詩注釋字體（utml-qts）作為替代方案：

（7）日文字符的分歧：

Adobe Japan 1-6 表外引入：

𢁉	0x22049	1153
𨓜	0x284DC	1203
宫	0x05BAB	1654
查	0x067E5	2090
值	0x0503C	2955
侣	0x04FA3	3967
吕	0x05415	4042
兑	0x05151	4211
俞	0x04FDE	4216
剎	0x0524E	4258
篡	0x07BE1	4330
喻	0x055BB	4411
毁	0x06BC1	4509
廄	0x05EC4	4747
弒	0x05F12	4769
拋	0x062CB	4973
摇	0x06447	5009
术	0x0672F	5141
榅	0x06985	5254
瑜	0x2F92F	5674
蕰	0x08570	6444
暨	0x066A8	7992
𧺯	0x27EAF	8630
鄉	0x09109	8637
巔	0x05DD4	13536
𤣩	0x248E9	13729
户	0x06237	13757
丢	0x04E22	14298
嗥	0x055E5	14399
噑	0x05651	14399
媮	0x05AAE	14461
媯	0x05AAF	14467
悮	0x060AE	14541
棁	0x068C1	14635
氲	0x06C32	14688
緼	0x07DFC	14988
翱	0x07FF1	15010
翶	0x07FF6	15010
醖	0x09196	15211
溈	0x06E88	16945
煴	0x07174	16963
塈	0x05848	17455
輼	0x08F3C	18751
𱍐（示）	0x31350	19130
揾	0x063FE	19410
軿	0x08EFF	19874
捝	0x0635D	21629
涚	0x06D9A	21869
蒀	0x08480	22349
豜	0x08C5C	22573

Adobe Japan 1-6 表内刪除（映射給 D3 思源字體（ut-min-l-symt））：

关	0x05173	关	0x05173
复	0x0590d	复	0x0590d
径	0x05f84	径	0x05f84
画	0x0753b	画	0x0753b
茎	0x0830e	茎	0x0830e
门	0x095E8	門(irregular)	0x095E8


Adobe Japan 1-6 與 Adobe Japan 1-5 我的私人映射方案上的差別：

Kanji	Unicode	Pr6N	ProV
僲	0x050F2	cid21164	cid16786
卥	0x05365	cid21235	cid19207
叠	0x053E0	cid21248	cid19219
夆	0x05906	cid21371	cid17469
槾	0x069FE	cid21791	cid17850
橕	0x06A55	cid21797	cid19467
葜	0x0845C	cid22341	cid18514
蕝	0x0855D	cid22372	cid19750
驊	0x09A4A	cid22920	cid15319


附錄：Adobe Japan 1-6 與 Adobe Japan 1-5 映射上的差別：

Kanji	Unicode	Pr6N	ProV
夆	0x5906	name="cid21371"	name="cid17469"
悞	0x609e	name="cid21558"	name="cid14541"
暵	0x66b5	name="cid21722"	name="cid17755"
煒	0x7152	name="cid21933"	name="cid14762"
譿	0x8b7f	name="cid21074"	name="cid08625"
驊	0x9a4a	name="cid22920"	name="cid15319"



6、所有的超大字符 jvf 均使用方正中華書局宋體 02／15 平面漢字；

下載地址（需要實名注冊登錄）：https://www.ancientbooks.cn/helpcore?font



7、其他的超大字符集，作者無法提供，請使用 Google 搜索查找。


九、致謝

感謝「霞鶩文楷」「霞鶩文楷 L」的開發者。

感謝「八登崇之」老師。沒有他的脚本就沒有這個虛擬字體。

感謝「齋藤修三郎」老師。

感謝「于無聲處」老師。


子康

2024 年 5 月 8 日

