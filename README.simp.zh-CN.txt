虚拟字体的通用设计

这一章讲虚拟字体通用设计，主旨是在 ujlreq 和 utmin10 的基础上，为简体中文直排配置完全覆盖 URO 和 BMP 汉字的标准虚拟字体。

综述

总体设计思路：

一、面向 dvipdfmx 的 mapline／jfm

呼号说明：up-min-l-ja	四个字段，

字段 1：排版方向
字段 2：字形描述
字段 3：字重，最重要的参数之一
字段 4：自定义。字段 4 与厂家无关。

字段 1：
up	横排；
ut	直排；

字段 2：
min	Mincho，明朝体；
go	Gothic，哥特体，这里活用做楷书、仿宋或者黑体
mg	MaruGothic，圆体
ks	kaisho，楷书字体
fs	fangsong，仿宋字体（宋朝体）


字段 3：
简写	名称			W 字重法描述	说明
t/ul	UltraLight/Thin		W100		极细体（未使用）
j	ExtraLight		W200		超细体（未使用）
l	Light			W300		细体
r	Regular			W400		常规
m	Medium			W500		中等（未使用）
d	DemiBold/Semibold	W600		半粗体（未使用）
b	Bold			W700		粗体
e	ExtremeBold		W800		超粗体
h/ub	UltraBold/Heavy/Black	W900		极粗体（未使用）
（非常规）
lb	Light-Bold		W300		半细体，细体的横笔加粗版，相当于 UD（未使用）
rb	Regular-Bold		W400		半常规，常规的横笔加粗版，相当于 UD（未使用）


字段 4（自定义）：

ja	表示日文基干
uro	当不映射思源时，盒子不会缩窄到 96％，此时使用 uro 作为呼号；
symt	表示映射给思源；注意：如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
cjka	CODESPACE 为 CJK-A
cjkb	CODESPACE 为 CJK-B
cjkc	CODESPACE 为 CJK-C
cjkd	CODESPACE 为 CJK-D（当物理字体仅覆盖到 CJK-D 时可用此配置为 CJK-E、CJK-F 单独指定物理字体）
cjke	CODESPACE 为 CJK-E
cjkf	CODESPACE 为 CJK-F
plane02	CODESPACE 为 02 平面
plane15	CODESPACE 为 15 平面
bd	标点（bd）：用作标点符号替换
hq	横排时标点参照，用作蝌蚪引号转置（重要）
fs	映射给仿宋
ks	映射给楷书
lxgw	映射给霞鹜文楷


二、map files

1、一般知识

字族名：tsuku

字族名即为文件夹之名称。

安装目录：

C:\texlive\texmf-local\fonts\map\dvipdfmx

安装方式：

整个文件夹直接拷贝过去，完成后使用管理员打开 CMD 执行 

mktexlsr

以及管理员执行（一共四条命令）

kanji-config-updmap-sys --jis2004 tsuku
kanji-config-updmap-sys --sc tsuku
kanji-config-updmap-sys --tc tsuku

文件说明：

otf-sc-tsuku.map	简体中文 CID
otf-tc-tsuku.map	繁体中文 CID
uptex-sc-tsuku.map	简体中文 URO
uptex-tc-tsuku.map	繁体中文 URO
otf-tsuku.map		日文 CID 并 UID（为 otf.sty 配置）
otf-up-tsuku.map	日文 URO（为 otf.sty 配置）
ptex-tsuku.map		日文 ISO-2022-JP（表外类推）
ptex-tsuku-04.map	日文 ISO-2022-JP（新字形）
uptex-tsuku.map		为 plain UpTeX 配置的 mapline
uptex-tsuku-04.map	为 upLaTeX 配置的 mapline，自定义设置全在这里面

2、map files 的结构

jfm 呼号  映射编码方式  实际字体  ［  转置参数   可选参数  ］

jfm ：jvf 中配置虚拟盒子时所使用字体代号，因此称之为「呼号」，仅仅只是一个代号，你也可以给它取个 「字母-9527」，尽量不要和系统上已有的 tfm（西文的 tfm）重名冲突。

编码映射方式，常见的有三种：

第一种是 Adobe Ordering，亦即

Adobe Ordering		地区		映射编码方式					opentype 的实现（举例）
Adobe Japan1-3		日语		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		Heisei Mincho Std W9.otf
Adobe Japan1-4		日语		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		IwaSouPro-Md.otf
Adobe Japan1-5		日语		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		HiraMinProN-W3.otf
Adobe Japan1-6		日语		UniJIS-UTF16-H／V；UniJIS-UCS2-H／V		A-OTF-RyuminPr6-Light.otf
Adobe Japan1-6		日语		UniJIS2004-UTF16-H／V；UniJIS-UCS2-H／V		A-OTF-RyuminPr6N-Light.otf
Adobe GB1-4		简体中文	UniGB-UTF16-H／V；UniGB-UCS2-H／V		AdobeSongStd-Light.otf
Adobe CNS1-3		繁体中文	UniCNS-UTF16-H／V；UniCNS-UCS2-H／V		AdobeFanHeitiStd-Bold.otf
Adobe-KR-2		朝鲜语／韩国语	UniKS-UTF16-H／V；UniKS-UCS2-H／V		MO-UDShinGoHangKo2-Reg.otf


除了 UTF16 还有 UTF32 也能映射，示例

\AtBeginDvi{
  \special{pdf:mapline r-up-min-r-ja   UniJISX02132004-UTF32-H  A-OTF-RyuminPr6N-Light.otf }%
}

能用，但没必要。

Adobe Ordering 的字体比较特别，dvipdfmx 不需要读字体的 cmap，只需要查 Adobe 的表就能完成字形的子集化嵌入。

第二种是 非 Adobe Ordering，哈哈，所有的 TTF 以及绝大多数国标 OTF 字体都属于这类。

横向 mapline 可以这样写

呼号	映射方式	物理字体
upbw-a    unicode    FZBWKSGBX.OTF

纵向的 mapline 要这样写

utbw-a    unicode    FZBWKSGBX.OTF  -w  1

说明，转置参数适用于非 Adobe Ordering 的字体，参数为  「 -w 1 」，意思是将汉字或全宽字符旋转 90°正置。

「jfm 呼号  映射编码方式  实际字体  ［  转置参数   可选参数  ］」

可选参数为 「 -v 10 」， 「-v 」 表示改变 stemV 値，値配置为 10；注意，在 Windows 系统上，数值配置的越小越黑，一般配置为 50／

第三种类型的，像是 Adobe 自己做的非规格字体，或者 思源字体，都有自己专用的映射链，视情况而定，有的仅有横向映射链，纵向时还是只能使用 unicode／此处按下不表，应用面窄也不消举例了。

3、mapfiles 的自定义修改

创建字族：

可将已有之字族复制到桌面再重命名，注意，目录本身的名称即为字族全名，然后在内部的每一个文件名上重复这个关键字。

例

拷贝 C:\texlive\2024\texmf-dist\fonts\map\dvipdfmx\ptex-fontmaps\kozuka-pr6n

至桌面，将 kozuka-pr6n 重命名为我的字族名 steve

然后将内部所有的文件名称全部重命名：

ren    otf-kozuka-pr6n.map    otf-steve.map
ren    otf-up-kozuka-pr6n.map    otf-up-steve.map
ren    ptex-kozuka-pr6n-04.map    ptex-steve-04.map
ren    ptex-kozuka-pr6n.map    ptex-steve.map
ren    uptex-kozuka-pr6n-04.map    uptex-steve-04.map
ren    uptex-kozuka-pr6n.map    uptex-steve.map

为每个 jfm 单独配置映射物理字体后，再将此文件目录整个拷贝到 C:\texlive\texmf-local\fonts\map\dvipdfmx

管理员 cmd 执行 mktexlsr 以及

kanji-config-updmap-sys --jis2004 steve




三、CID 安装

管理员打开 CMD，然后输入

cd C:\texlive\2024\texmf-dist\scripts\cjk-gs-integrate

切换目录到 cjk-gs-integrate 目录下

先清理 CID 的旧缓存，执行

perl cjk-gs-integrate.pl --cleanup

再执行

perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local

1、如果出现错误，错误内容为「已存在 CIDFONT」（意译），则根据提示找到 CIDFONT 所在的目录，手动删除，再次执行 「perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local」

在 README 中：

perl cjk-gs-integrate.pl --link-texmf C:\texlive\texmf-local

（ 在 TL2024 中，在一些情况下要加 --force 参数。因为部分字体文件在 C:/windows/Fonts 目录 和 TL 的字体目录中，存在相同 PS 名称，即使手动删除 CIDFONT 所在的目录，也会存在 「已存在 CIDFONT」错误。）



2、如果出现错误，错误内容为 Perl 不是可执行的程序。

解决方法一：安装 ActivePerl-5.28.1.2801-MSWin32-x64-24563874.exe

解决方法二：安装 texlive2024 后，为了能够直接运行 perl 脚本有若干种方法。

把 texlive 自带的 perl 加入设置系统环境变量：

加入方法是在 变量名：PATH 中增加

“C:\texlive\2024\tlpkg\tlperl\bin”

由于 texlive2024 把 perl 解释器的版本升级为 perl5 (revision 5 version 38 subversion 2)，在中文版 windows 系统中使用会报错：Locale 'Chinese (Simplified)_China.936' is unsupported, and may crash the interpreter.（繁体中文下为 cpc950）

原因是系统语言为中文（运行：systeminfo | findstr /B /C:"System Locale" 会显示：System Locale: zh-cn;Chinese (China)，说明系统环境是中文环境）

为了避免这个错误，需要添加系统环境变量：

变量名：LANG 
变量值：zh_CN.UTF-8

（繁体的话，可类推 zh_TW.UTF-8(?)来个小白鼠验证一下）

重启后生效。

方法三： Strawberry Perl （http://strawberryperl.com/）

为了和 texlive 默认环境保持一致，推荐下载 Perl 5.38.2.x 版本。
然后，在 C:\texlive\2024\texmf.cnf 中，将 TEXLIVE_WINDOWS_TRY_EXTERNAL_PERL = 0 改为 TEXLIVE_WINDOWS_TRY_EXTERNAL_PERL = 1
以便启用新安装的 perl 解释器。

四、NFSS 示例

```
% mincho light; 明朝体，细
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

% gothic regular; 黑体，常规
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
% 圆体，常规
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
% mincho light; 明朝体，细
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

% gothic regular; 黑体，常规
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
% 圆体，常规
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

在 NFSS 中配置了 jvf，而 jfm 的映射链交由 map files 来处理，这里不需要再配置 AtBeginDvi



五、面向 NFSS／jvf （这部分需要与下文对看）

总体规划

utstone-jan	jlreq 横排 100％ 宽幅标点符号，相邻标点 150％ 宽幅；直排 100％ 宽幅，相邻标点 150％ 宽幅；映射非思源字体，不挤压汉字盒子
utstone-feb	jlreq 横排 100％ 宽幅标点符号，相邻标点 150％ 宽幅；直排 100％ 宽幅，相邻标点 150％ 宽幅；映射思源字体，要将盒子横向挤压 96％；
utstone-mars	jlreq 横排 75％ 宽幅标点符号，相邻标点 125％ 宽幅；直排 50％ 宽幅，相邻标点 100％ 宽幅；映射非思源字体，不挤压汉字盒子
utstone-apr	jlreq 横排 75％ 宽幅标点符号，相邻标点 125％ 宽幅；直排 50％ 宽幅，相邻标点 100％ 宽幅；映射思源字体，要将盒子横向挤压 96％；
utstone-vert	行间标点：jlreq 横排 75％ 宽幅标点符号，相邻标点 125％ 宽幅；映射思源字体，要将盒子横向挤压 96％；行间标点符号，转置后著红色。暂定转置方案为：成对标点符号转为 0 宽角引号。
utstone-qts	用作全唐诗的行间标点方案，成对标点符号不转 0 宽，保留括弧，一律半宽。仅直排，单一字重。标点符号转读点，著红色。
utstone-jun	割注字体：仅有直排：全宽标点符号，有 gluekern，相邻标点 150％ 宽幅，明体风格 L／M 两字重，割注字体。映射思源字体，要将盒子横向挤压 96％；
utstone-jul	单行小注字体：仅有直排：50％ 宽幅标点符号，相邻标点 100％ 宽幅；明体风格 L／M 两字重，仿宋、楷体风格各一字重，单行小字注释。映射思源字体，要将盒子横向挤压 96％；




设计内容

一、以 ujlreq-v 为模板，直排时的通用设计

utml-apr
utmr-apr
utmb-apr
utmg-apr
utgr-apr
utgb-apr
utge-apr



utml-apr.zvp

序号	呼号（新）	说明
D0	ut-min-l-ja	D0 表示 0 区，默认汉字字体：u-Unicode，t 表示垂直，ut 表示垂直（up 表示横排）；min 表示 mincho，明朝体；l-Light 字重；ja 表示默认为日语
D3	ut-min-l-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	ut-min-l-cjka	CJK-A；自定义
D5	ut-min-l-cjkb	CJK-B；ut 方正书局宋 L-02 平面
D6	ut-min-l-cjkc	CJK-C；ut 方正书局宋 L-02 平面
D7	ut-min-l-cjkd	CJK-D；ut 方正书局宋 L-02 平面（当物理字体仅覆盖到 CJK-D 时可用此配置为 CJK-E、CJK-F 单独指定物理字体）
D8	ut-min-l-cjke	CJK-E；ut 方正书局宋 L-02 平面
D9	ut-min-l-cjkf	CJK-F；ut 方正书局宋 L-02 平面
D10	ut-min-l-plane15	S-PUA:15 区汉字;ut 方正书局宋 L-15 平面
D11	ut-min-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-min-l-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjml-v	c 表示 cid，j 表示 Japanese，m 表示明朝，l-Light 字重（映射必须与 D0 完全一致）此字体用作内部映射，不参与外部映射。


utmr-apr.zvp

D0	ut-min-r-ja	D0 表示 0 区，默认汉字字体：u-Unicode，t 表示垂直，ut 表示垂直（up 表示横排）；min 表示 mincho，明朝体；r-Regular 字重；ja 表示默认为日语
D3	ut-min-r-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	ut-min-r-cjka	CJK-A；自定义
D5	ut-min-r-cjkb	CJK-B；ut 方正书局宋 L-02 平面
D6	ut-min-r-cjkc	CJK-C；ut 方正书局宋 L-02 平面
D7	ut-min-r-cjkd	CJK-D；ut 方正书局宋 L-02 平面
D8	ut-min-r-cjke	CJK-E；ut 方正书局宋 L-02 平面
D9	ut-min-r-cjkf	CJK-F；ut 方正书局宋 L-02 平面
D10	ut-min-r-plane15	S-PUA:15 区汉字;ut 方正书局宋 L-15 平面
D11	ut-min-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-min-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmr-v	c 表示 cid，j 表示 Japanese，m 表示明朝，r-Regular 字重（映射必须与 D0 完全一致）此字体用作内部映射，不参与外部映射。

utmb-apr.zvp

D0	ut-min-b-ja	D0 表示 0 区，默认汉字字体：u-Unicode，t 表示垂直，ut 表示垂直（up 表示横排）；min 表示 mincho，明朝体；b-Bold 字重；ja 表示默认为日语
D3	ut-min-b-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	ut-min-b-cjka	CJK-A；自定义
D5	ut-min-b-cjkb	CJK-B；ut 方正书局宋 L-02 平面
D6	ut-min-b-cjkc	CJK-C；ut 方正书局宋 L-02 平面
D7	ut-min-b-cjkd	CJK-D；ut 方正书局宋 L-02 平面
D8	ut-min-b-cjke	CJK-E；ut 方正书局宋 L-02 平面
D9	ut-min-b-cjkf	CJK-F；ut 方正书局宋 L-02 平面
D10	ut-min-b-plane15	S-PUA:15 区汉字;ut 方正书局宋 L-15 平面
D11	ut-min-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-min-b-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmb-v	c 表示 cid，j 表示 Japanese，m 表示明朝，b-Bold 字重（映射必须与 D0 完全一致）


utmg-apr.zvp

D0	ut-mg-r-ja	D0 表示 0 区，默认汉字字体：ut 表示垂直，mg 表示 MaruGothic 圆体，r-Regular 字重；ja 表示默认为日语
D3	ut-mg-r-rd	D3 表示 D0 在 BMP 上汉字的补集：rd 圆体的意思；
D4	ut-mg-r-cjka	CJK-A；自定义
D5	ut-mg-r-plane02		02 平面；ut 方正书局宋 L-02 平面（仅用作 fallback）
D11	ut-mg-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-mg-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmg-v	c 表示 cid，j 表示 Japanese，mg 表示 MaruGothic 圆体，r-Regular 字重，（映射必须与 D0 完全一致）


utgr-apr.zvp

D0	ut-go-r-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：r-Regular 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D3	ut-go-r-ks	D3 表示 D0 在 BMP 上汉字的补集：映射为 DF 仿宋 GBA Std W3；fs-表示仿宋
D4	ut-go-r-cjka	CJK-A；自定义
D5	ut-go-r-cjkb	CJK-B；汉仪仿宋 CJK-B(HYFangSong_GB18030Super-ExtB)
D6	ut-go-r-cjkc	CJK-C；ut 方正书局宋 L-02 平面
D7	ut-go-r-cjkd	CJK-D；ut 方正书局宋 L-02 平面（当物理字体仅覆盖到 CJK-D 时可用此配置为 CJK-E、CJK-F 单独指定物理字体）
D8	ut-go-r-cjke	CJK-E；ut 方正书局宋 L-02 平面
D9	ut-go-r-cjkf	CJK-F；ut 方正书局宋 L-02 平面
D10	ut-go-r-plane15	S-PUA:15 区汉字;ut 方正书局宋 L-15 平面（如果这里不配置，则需手动切换到明体 R。因为没有替代，所以让方正书局宋 15 平面充当临时演员）
D11	ut-go-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-go-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjgr-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，r-Regular 字重（映射必须与 D0 完全一致）


utgb-apr.zvp

D0	t-go-b-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：b-Bold 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D3	t-go-b-ks	D3 表示 D0 在 BMP 上汉字的补集：映射为 DF 楷书 GBA Std W5；ks-表示楷书
D4	t-go-b-cjka	CJK-A；自定义
D5	t-go-b-cjkb	CJK-B；方正楷体 S-SIP：FZKaiS(SIP).TTF
D6	t-go-b-cjkc	CJK-C；ut 方正书局宋 L-02 平面
D7	t-go-b-cjkd	CJK-D；ut 方正书局宋 L-02 平面（当物理字体仅覆盖到 CJK-D 时可用此配置为 CJK-E、CJK-F 单独指定物理字体）
D8	t-go-b-cjke	CJK-E；ut 方正书局宋 L-02 平面
D9	t-go-b-cjkf	CJK-F；ut 方正书局宋 L-02 平面
D10	ut-go-b-plane15	S-PUA:15 区汉字;ut 方正书局宋 L-15 平面（如果这里不配置，则需手动切换到明体 R。因为没有替代，所以让方正书局宋 15 平面充当临时演员）
D11	t-go-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	t-go-b-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	tf-cjgb-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，b-Bold 字重（映射必须与 D0 完全一致）


utge-apr.zvp

D0	ut-go-e-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：e-ExtremeBold 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D3	ut-go-e-ks	D3 表示 D0 在 BMP 上汉字的补集；粗体楷书字体
D4	ut-go-e-cjka	CJK-A；自定义
D5	ut-go-e-plane02		02 平面；ut 方正书局宋 L-02 平面（仅用作 fallback）
D11	ut-go-e-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	ut-go-e-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjge-v	c 表示 cid，j 表示 Japanese，g 表示 Gothic，ExtraBold 字重（映射必须与 D0 完全一致）


二、以 ujlreq 为模板，横排时的通用设计

upml-apr
upmr-apr
upmb-apr
upmg-apr
upgr-apr
upgb-apr
upge-apr


upml-apr.zvp

序号	呼号（新）	说明
D0	up-min-l-ja	D0 表示 0 区，默认汉字字体：u-Unicode，up 表示横排；min 表示 mincho，明朝体；l-Light 字重；ja 表示默认为日语
D2	up-min-l-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-min-l-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	up-min-l-cjka	CJK-A；自定义；如果映射的 uro 字体是 GBK 时，设置本项目为 CJK-A 的替字。如果映射的 uro 字体是 GB18030 的，将本项目设置为与 uro(symt)相同。
D5	up-min-l-cjkb	CJK-B；up 方正书局宋 L-02 平面
D6	up-min-l-cjkc	CJK-C；up 方正书局宋 L-02 平面
D7	up-min-l-cjkd	CJK-D；up 方正书局宋 L-02 平面
D8	up-min-l-cjke	CJK-E；up 方正书局宋 L-02 平面
D9	up-min-l-cjkf	CJK-F；up 方正书局宋 L-02 平面
D10	up-min-l-plane15	S-PUA:15 区汉字;up 方正书局宋 L-15 平面
D11	up-min-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-min-l-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjml-h	c 表示 cid，j 表示 Japanese，m 表示明朝，l-Light 字重（映射必须与 D0 完全一致）此字体用作内部映射，不参与外部映射。


upmr-apr.zvp

D0	up-min-r-ja	D0 表示 0 区，默认汉字字体：u-Unicode，up 表示横排；min 表示 mincho，明朝体；r-Regular 字重；ja 表示默认为日语
D2	up-min-r-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-min-r-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	up-min-r-cjka	CJK-A；自定义
D5	up-min-r-cjkb	CJK-B；up 方正书局宋 L-02 平面
D6	up-min-r-cjkc	CJK-C；up 方正书局宋 L-02 平面
D7	up-min-r-cjkd	CJK-D；up 方正书局宋 L-02 平面
D8	up-min-r-cjke	CJK-E；up 方正书局宋 L-02 平面
D9	up-min-r-cjkf	CJK-F；up 方正书局宋 L-02 平面
D10	up-min-r-plane15	S-PUA:15 区汉字;up 方正书局宋 L-15 平面
D11	up-min-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-min-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmr-h	c 表示 cid，j 表示 Japanese，m 表示明朝，r-Regular 字重（映射必须与 D0 完全一致）此字体用作内部映射，不参与外部映射。

upmb-apr.zvp

D0	up-min-b-ja	D0 表示 0 区，默认汉字字体：u-Unicode，up 表示横排；min 表示 mincho，明朝体；b-Bold 字重；ja 表示默认为日语
D2	up-min-b-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-min-b-symt	D3 表示 D0 在 BMP 上汉字的补集：思源明朝；同前；如果映射思源字体，要将盒子横向挤压 96％；如果映射的不是思源，则不需要挤压，这里产生分歧
D4	up-min-b-cjka	CJK-A；自定义
D5	up-min-b-cjkb	CJK-B；up 方正书局宋 L-02 平面
D6	up-min-b-cjkc	CJK-C；up 方正书局宋 L-02 平面
D7	up-min-b-cjkd	CJK-D；up 方正书局宋 L-02 平面
D8	up-min-b-cjke	CJK-E；up 方正书局宋 L-02 平面
D9	up-min-b-cjkf	CJK-F；up 方正书局宋 L-02 平面
D10	up-min-b-plane15	S-PUA:15 区汉字;up 方正书局宋 L-15 平面
D11	up-min-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-min-b-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmb-h	c 表示 cid，j 表示 Japanese，m 表示明朝，b-Bold 字重（映射必须与 D0 完全一致）


upmg-apr.zvp

D0	up-mg-r-ja	D0 表示 0 区，默认汉字字体：up 表示垂直，mg 表示 MaruGothic 圆体，r-Regular 字重；ja 表示默认为日语
D2	up-mg-r-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-mg-r-rd	D3 表示 D0 在 BMP 上汉字的补集：rd 圆体的意思；
D4	up-mg-r-cjka	CJK-A；自定义
D5	up-mg-r-plane02		02 平面；up 方正书局宋 L-02 平面（仅用作 fallback）
D11	up-mg-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-mg-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjmg-h	c 表示 cid，j 表示 Japanese，mg 表示 MaruGothic 圆体，r-Regular 字重，（映射必须与 D0 完全一致）


upgr-apr.zvp

D0	up-go-r-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：r-Regular 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D2	up-go-r-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-go-r-ks	D3 表示 D0 在 BMP 上汉字的补集：映射为 DF 仿宋 GBA Std W3；fs-表示仿宋
D4	up-go-r-cjka	CJK-A；自定义
D5	up-go-r-cjkb	CJK-B；汉仪仿宋 CJK-B(HYFangSong_GB18030Super-ExtB)
D6	up-go-r-cjkc	CJK-C；up 方正书局宋 L-02 平面
D7	up-go-r-cjkd	CJK-D；up 方正书局宋 L-02 平面
D8	up-go-r-cjke	CJK-E；up 方正书局宋 L-02 平面
D9	up-go-r-cjkf	CJK-F；up 方正书局宋 L-02 平面
D10	up-go-r-plane15	S-PUA:15 区汉字;up 方正书局宋 L-15 平面（如果这里不配置，则需手动切换到明体 R。因为没有替代，所以让方正书局宋 15 平面充当临时演员）
D11	up-go-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-go-r-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjgr-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，r-Regular 字重（映射必须与 D0 完全一致）


upgb-apr.zvp

D0	up-go-b-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：b-Bold 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D2	up-go-b-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-go-b-ks	D3 表示 D0 在 BMP 上汉字的补集：映射为 DF 楷书 GBA Std W5；ks-表示楷书
D4	up-go-b-cjka	CJK-A；自定义
D5	up-go-b-cjkb	CJK-B；方正楷体 S-SIP：FZKaiS(SIP).TTF
D6	up-go-b-cjkc	CJK-C；up 方正书局宋 L-02 平面
D7	up-go-b-cjkd	CJK-D；up 方正书局宋 L-02 平面
D8	up-go-b-cjke	CJK-E；up 方正书局宋 L-02 平面
D9	up-go-b-cjkf	CJK-F；up 方正书局宋 L-02 平面
D10	up-go-b-plane15	S-PUA:15 区汉字;up 方正书局宋 L-15 平面（如果这里不配置，则需手动切换到明体 R。因为没有替代，所以让方正书局宋 15 平面充当临时演员）
D11	up-go-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-go-b-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjgb-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，b-Bold 字重（映射必须与 D0 完全一致）


upge-apr.zvp

D0	up-go-e-ja	D0 表示 0 区，默认汉字字体：go-Gothic 黑体，黑体字族的呼号：e-ExtremeBold 字重；u 表示 Unicode，t 表示直排；ja 表示默认为日语
D2	up-go-e-hq	标点符号字体，用于在半宽宽度下输出全宽蝌蚪引号。
D3	up-go-e-ks	D3 表示 D0 在 BMP 上汉字的补集；粗体楷书字体
D4	up-go-e-cjka	CJK-A；自定义
D5	up-go-e-plane02		02 平面；up 方正书局宋 L-02 平面（仅用作 fallback）
D11	up-go-e-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D12	up-go-e-bd	标点符号替换字体：把 D0 的标点符号更换为 D10，bd-标点的意思
D19	otf-cjge-h	c 表示 cid，j 表示 Japanese，g 表示 Gothic，ExtraBold 字重（映射必须与 D0 完全一致）


三、以 utmin10 为模板，直排时的通用设计

设计思路：此字体是 utmin10/umin10 的简化版，且不考虑 Plane02 生僻字。专门用于支持 URO 的简体汉字。适合单独配置为 GBK/GB18030-2000 规格的简体中文字体。繁体中文、日文不适用。

utgb-min-l

D0	r-ut-min-l-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-min-l-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-ut-min-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

utgb-min-r

D0	r-ut-min-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-min-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-ut-min-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjmr-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

utgb-min-b

D0	r-ut-min-b-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-min-b-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-ut-min-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjmb-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）


utgb-mg-r

D0	r-ut-mg-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-mg-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmg-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

utgb-go-r

D0	r-ut-go-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-go-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmr-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

utgb-go-b

D0	r-ut-go-b-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-go-b-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmb-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

utgb-go-e

D0	r-ut-go-e-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-go-e-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjme-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）



utgb-fs-l.zvp

窄体 86％（长仿宋）

D0	r-ut-min-l-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-fs-l-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D4	r-ut-fs-l-plane02	用作映射 plane02 汉字，以 CTRANGE 指定汉字的范围。
D11	r-ut-fs-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）



utgb-ks-l.zvp

正度楷体，100％ 宽度不缩放，CJK-B

D0	r-ut-min-l-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D3	r-ut-ks-l-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D4	r-ut-ks-l-plane02	用作映射 plane02 汉字，以 CTRANGE 指定汉字的范围。
D11	r-ut-ks-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjml-v	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）





四、以 umin10 为模板，横排时的通用设计


upgb-min-l

D0	r-up-min-l-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-min-l-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-min-l-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-up-min-l-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjml-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

upgb-min-r

D0	r-up-min-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-min-r-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-min-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-up-min-r-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjmr-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

upgb-min-b

D0	r-up-min-b-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-min-b-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-min-b-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D11	r-up-min-b-lxgw	霞鹜文楷 L：用作伏羲六十四卦
D19	otf-cjmb-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）


upgb-mg-r

D0	r-up-mg-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-mg-r-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-mg-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmg-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

upgb-go-r

D0	r-up-go-r-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-go-r-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-go-r-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmr-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

upgb-go-b

D0	r-up-go-b-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-go-b-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-go-b-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjmb-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）

upgb-go-e

D0	r-up-go-e-ja	日文基干，字体的主体，用作标点和杂项符号的映射
D2	r-up-go-e-hq	用作标点符号，半宽宽度下输出全宽蝌蚪引号。
D3	r-up-go-e-uro	用作映射 URO 汉字，以 CTRANGE 指定汉字的范围。
D19	otf-cjme-h	日文 CID，用作标点符号的转置。（映射必须与 D0 完全一致）


五、垂直行间标点符号

1、以 ujlreq-v 为模板，不输出成对标点符号（即前 x 号、后 x 号），标点转读点，著红色

呼号与 utmr 系列相同，唯标点部分有差。

改订方案：成对标点符号均被改为 Adobe Japan1-4 中的 cid12156 和 cid12157 。

min-l 和 min-r 兩個，都做了 96％ 96％ 的縮放，以實現在不改變模板的前提下拉伸字距。



著色办法（颜色堆栈）：

(CHARACTER H 3002
   (MAP
      (SPECIAL color push cmyk 0 0.85 0.85 0)
      (MOVEUP R 0.22)
      (MOVERIGHT R -0.35)
      (SETCHAR H 3002)
      (SPECIAL color pop)
      )
   )


2、以 ujlreq-v 为模板，不输出成对标点符号（即前 x 号、后 x 号），标点转读点，不著色

呼号与 utmr 系列相同，唯标点部分有差。鸽了。此方案已被废弃。


3、以 ujlreq-v 为模板，输出成对标点符号（嵌入行中，与竖排无异），行间标点不著色

呼号与 utmr 系列相同，唯标点部分有差。此方案已改为 4 中的（1）全唐诗注释。

4、全唐诗注释和全唐诗小字注释

（1）全唐诗注释呼号与 utmin10 相同，且增加生僻字处理。

改订方案：成对标点符号均被改为 Adobe Japan1-4 中的 cid12156 和 cid12157 。额外的，由于全唐诗使用校勘符号（〔〕（））以及书名号（《》〈〉）不能被批量替为角引号，所以做了插入行间的处理。

（2）全唐诗小字注释与 utmin10 相同，且增加生僻字处理。另外附加向右侧的偏移向量

5、全角割注字体

呼号与 utmr 系列相同，唯 GLUEKERN 部分有差


6、单行小注字体

呼号与 utmr 系列相同，唯 GLUEKERN 部分有差；另外附加向右侧的偏移向量

7、霞鹜文楷虚拟字体

呼号与 utmin10 系列相同，D0 部分替换为 Klee-Pro，且包含生僻字和康熙字典体。此方案已修改。

改为直接使用 URO 的模板。唯标点符号仍用日文 Klee-Pro／



六、全宽标点符号虚拟字体（横排并直排）



七、横排 80％ 宽幅挤压的非标虚拟字体（irregular）

直排只做两种，一种 50％ 宽幅，一种 100％ 宽幅。此方案已修改。

横排只做 75％ 和全宽两种。先做 utstone-apr，其余全部依次复制。

八、已知存在的问题


1、0x00B7

在简体中文排版中使用 0x00B7 作为中间间断点代替 0x30FB ，在 word 排版的中文文本中没有问题，但是！！但是，在 LaTeX 下是灾难性的。

这将导致中文文本中所有 0x00B7 都变成变宽字符。起初没有任何改良办法，只能手动把 0x00B7 替换成 0x30FB 当然我也推荐这样使用。

《诗・雅・鹿鸣》《文选・呉都赋》……

改良方案，在 jvf 中单独为 0x00B7 设置了 标点符号 Type 表，ID 为 15

(CHARSINTYPE D 15
   U00B7
   )

横排方案：字宽设为 0.333（1/3 宽字符 cid09778→0x2632）

(TYPE D 15
   (CHARWD R 0.333)
   (CHARHT R 0.88)
   (CHARDP R 0.12)
   (MAP
      (SELECTFONT D 19)
      (SETCHAR H 2632)
      )
   )

竖排方案：字宽设为 0.333（1/3 宽字符 cid13294→0x33EE）

(TYPE D 15
   (CHARWD R 0.333)
   (CHARHT R 0.5)
   (CHARDP R 0.5)
   (MAP
      (SELECTFONT D 19)
      (SETCHAR H 33EE)
      )
   )



2、竖排时使用割注虚拟字体 utstone-jun 会优先调用 JY2 而不是 JT2 


改了割注虚拟字体的配置，于是割注产生了变化。另外 TexLive2024 在竖排时，如果有 JY2 字体，可能会优先使用 JY2 ，导致竖排割注的 gluekern 失效！！如果没有 JY2 才会使用 JT2 需要留意！！！

这样的话，当局部切换到横排时会缺失横排字体，如果其中有割注将产生致命错误！当然，没有谁会用横排做割注吧？如果有，到时候需要用 \ifydir \iftdir 临时切替一下，示例如下：

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


3、自定义字体（造字）怎么加入虚拟字体？

使用 afdko 的 python 小程序 fonttools 取出造字的 cmap，

ttx -t cmap foo.ttf

然后提取汉字的 UID（ glyph name 是不必要的）


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

将以上代码格式化为

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

再改为

UE001 UE002 UE003 UE004 U2165F U23617 U25BE8 U25DB4 U27114 U27452 U274AF U2771F U2783A U27CFD U29859 U2985F U29CB2 UF0B23 



在示例文件 myfont-m-jt2.zvp 和 myfont-m-jy2.zvp 中对自造汉字进行了简短的定义。

找到 Source 里面的 myfont-utmin10  创建 jvf 


perl  jfmutil.pl  zvp2vf -u   --lenient  myfont-m-jt2
perl  jfmutil.pl  zvp2vf -u   --lenient  myfont-m-jy2

找到 Source 里面的 jfm-copy 复制 jfm

copy  urml.tfm    r-myfont-m-jy2.tfm
copy  urmlv.tfm   r-myfont-m-jt2.tfm

安装 tfm

r-myfont-m-jy2.tfm  →  C:\texlive\texmf-local\fonts\tfm\［自定义创建一个 jfm 目录］
r-myfont-m-jt2.tfm  →  C:\texlive\texmf-local\fonts\tfm\［自定义创建一个 jfm 目录］

安装 jvf

jfm：

myfont-m-jy2.tfm  →  C:\texlive\texmf-local\fonts\tfm\myfont
myfont-m-jt2.tfm  →  C:\texlive\texmf-local\fonts\tfm\myfont

jvf：

myfont-m-jy2.vf  →  C:\texlive\texmf-local\fonts\vf\myfont
myfont-m-jt2.vf  →  C:\texlive\texmf-local\fonts\vf\myfont

复制完，使用管理员打开 CMD 执行 mktexlsr

NFSS 配置：

%%% myfont ファミリーに m と db のシリーズを定义
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

在 LaTeX 中使用它需要 pxbase 包（\usepackage{pxbase}）支持使用 \UI{XXXX} 取出汉字：


正文正文正文{\myfont\UI{2165F}}正文正文正文


4、怎么切换 NFSS 的配置，比方说，我想把红楼梦模板的虚拟字体切换成 jlreq 的配置

切换方法简单粗暴，直接批量替换

试查找：「-apr」

替换为：「-feb」

注意总体设计里头的几种虚拟字体方案，当你配置 NFSS 时，一般只选取其中一种：用思源或者不用思源，横排挤压方案用 75％还是 100％，直排挤压方案用 50％还是 100％

可以直接改 NFSS 引用 jvf 的后缀来达到切换配置文件的目的。


utstone-jan	jlreq 横排 100％ 宽幅标点符号，相邻标点 150％ 宽幅；直排 100％ 宽幅，相邻标点 150％ 宽幅；映射非思源字体，不挤压汉字盒子
utstone-feb	jlreq 横排 100％ 宽幅标点符号，相邻标点 150％ 宽幅；直排 100％ 宽幅，相邻标点 150％ 宽幅；映射思源字体，要将盒子横向挤压 96％；
utstone-mars	jlreq 横排 75％ 宽幅标点符号，相邻标点 125％ 宽幅；直排 50％ 宽幅，相邻标点 100％ 宽幅；映射非思源字体，不挤压汉字盒子
utstone-apr	jlreq 横排 75％ 宽幅标点符号，相邻标点 125％ 宽幅；直排 50％ 宽幅，相邻标点 100％ 宽幅；映射思源字体，要将盒子横向挤压 96％；




5、我安装了 map_files、JFM、JVF，也执行了 mktexlsr ，也执行了 kanji-config-updmap-sys --jis2004 tsuku （kanji-config-updmap-sys --sc tsuku 以及 kanji-config-updmap-sys --sc tsuku）等的全部四条命令，也按要求配置了 cjk-gs-integrate，所有配置时都 no error 但为什么无法输出 PDF：提示找不到 真实物理字体：

说明：此虚拟字体配置方案的 map 分两种，一种是 Windows 下的，一种是 OSX 的，没有为 Linux 设计虚拟字体。

本配置方案不提供任何物理字体。 mapline 中使用的商业字体，在首次安装时，必须被替换为客户机上的客户字体。即是，用户必须尝试为 uplatex 配置为本地字体。毕竟配置文件只是配置文件嘛。

配置方法，查找替换。

试查找：「KaitiSC-Black.ttf」

替换为：「STKAITI.TTF」

简单粗暴，也很实用。


特别需要注意的是：

（1）所有的标点符号字体（-bd／biaodian／punctuations／约物）都是使用了日文 Pro（Adobe Japan 1-4／15444cid）及以上的日文字体，替换的时候不要替换成 Std／StdN（Adobe Japan 1-3／9354cid）的日文字体，标点符号会丢失。

（2）所有的日文明体字体（-ja／mincho／明朝体）都是按日文 Pr6／Pr6N（Adobe Japan 1-6／23058cid）配置的，替换过后的字体也必须是 Pr6／Pr6N，否则会丢失汉字。

（3）所有的日文哥特字体（-go／gothic／黑体）都是按日文 Pr5（Adobe Japan 1-5／20316cid）配置的，替换过后的字体也必须是 Pr5／Pr5N，否则会丢失汉字。

（4）所有的 uro 字体（-uro／Unicode）都是按 URO［(CTRANGE H 4E00 H 9FFF)］配置的，其中，0x9fa5（龥，不含）之后 0x9fa6（龦）到 0x9fff（鿿）的汉字，在 [_GBK] 后缀或者 [_GB18030] 后缀的字体中一般都不会配置，在 LaTeX 中输出失败也不是虚拟字体的问题，是物理字体本身没那个字。解决办法：使用思源字体。但是思源也没有 URO 末端最后的几个字。

（5）所有的 Plane02 字体，都按 CJK 分区做了拆分，以便客户单独配置每个分区所映射的物理字体。

（6）垂直行间标点虚拟字体（utstone-vert）不能正确输出校勘符号（〔〕（））以及书名号（《》〈〉），会直接转为角引号。当使用校勘符号时，不要使用垂直行间标点虚拟字体！！！可以使用全唐诗注释字体（utml-qts）作为替代方案：

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
刹	0x0524E	4258
篡	0x07BE1	4330
喻	0x055BB	4411
毁	0x06BC1	4509
厩	0x05EC4	4747
弑	0x05F12	4769
抛	0x062CB	4973
摇	0x06447	5009
术	0x0672F	5141
榅	0x06985	5254
瑜	0x2F92F	5674
蕰	0x08570	6444
暨	0x066A8	7992
𧺯	0x27EAF	8630
乡	0x09109	8637
巅	0x05DD4	13536
𤣩	0x248E9	13729
户	0x06237	13757
丢	0x04E22	14298
嗥	0x055E5	14399
噑	0x05651	14399
媮	0x05AAE	14461
妫	0x05AAF	14467
悮	0x060AE	14541
棁	0x068C1	14635
氲	0x06C32	14688
缊	0x07DFC	14988
翱	0x07FF1	15010
翶	0x07FF6	15010
酝	0x09196	15211
沩	0x06E88	16945
煴	0x07174	16963
塈	0x05848	17455
辒	0x08F3C	18751
𱍐（示）	0x31350	19130
揾	0x063FE	19410
𫐌	0x08EFF	19874
捝	0x0635D	21629
涚	0x06D9A	21869
蒀	0x08480	22349
豜	0x08C5C	22573

Adobe Japan 1-6 表内删除（映射给 D3 思源字体（ut-min-l-symt））：

关	0x05173	关	0x05173
复	0x0590d	复	0x0590d
径	0x05f84	径	0x05f84
画	0x0753b	画	0x0753b
茎	0x0830e	茎	0x0830e
门	0x095E8	门(irregular)	0x095E8


Adobe Japan 1-6 与 Adobe Japan 1-5 我的私人映射方案上的差别：

Kanji	Unicode	Pr6N	ProV
僲	0x050F2	cid21164	cid16786
卥	0x05365	cid21235	cid19207
叠	0x053E0	cid21248	cid19219
夆	0x05906	cid21371	cid17469
槾	0x069FE	cid21791	cid17850
橕	0x06A55	cid21797	cid19467
葜	0x0845C	cid22341	cid18514
𫈵	0x0855D	cid22372	cid19750
骅	0x09A4A	cid22920	cid15319


附录：Adobe Japan 1-6 与 Adobe Japan 1-5 映射上的差别：

Kanji	Unicode	Pr6N	ProV
夆	0x5906	name="cid21371"	name="cid17469"
悮	0x609e	name="cid21558"	name="cid14541"
暵	0x66b5	name="cid21722"	name="cid17755"
炜	0x7152	name="cid21933"	name="cid14762"
譿	0x8b7f	name="cid21074"	name="cid08625"
骅	0x9a4a	name="cid22920"	name="cid15319"



6、所有的超大字符 jvf 均使用方正中华书局宋体 02／15 平面汉字；

下载地址（需要实名注册登录）：https://www.ancientbooks.cn/helpcore?font



7、其他的超大字符集，作者无法提供，请使用 Google 搜索查找。


九、致谢


感谢「霞鹜文楷」「霞鹜文楷 L」的开发者。

感谢「八登崇之」老师。没有他的脚本就没有这个虚拟字体。

感谢「斋藤修三郎」老师。

感谢「于无声处」老师。


子康

2024 年 5 月 8 日

