---
layout: post
title: Android-L SIM卡自适应更新语言问题
permalink: android-sim
date: 2016-11-01 00:42:31
categories: Android
tags: Android
desc: 每次到发布UAT和Release 版本的时候，不同的组件总是出现各种问题，在测试中版本中就没有测试出来，就在今天发布Global（海外）版的时候遇到了一个之前并没发现的问题，就是刷机的rom版本是Global版的，默认的系统语言是英文版的。刚刷完机，插上中国区域的SIM 卡，第一次开机时候，语言自动切换到中文了，由于之前也没这方面经验，对这并不清楚，然后就当BUG 让我修改，后来在网上查了下资料，才了解MTK平台 确实有这么回事，Mark一下~详情如下：
---

## **前言**
每次到发布UAT和Release 版本的时候，不同的组件总是出现各种问题，在测试中版本中就没有测试出来，就在今天发布Global（海外）版的时候遇到了一个之前并没发现的问题，就是刷机的rom版本是Global版的，默认的系统语言是英文版的。刚刷完机，插上中国区域的SIM 卡，第一次开机时候，语言自动切换到中文了，由于之前也没这方面经验，对这并不清楚，然后就当BUG 让我修改，后来在网上查了下资料，才了解MTK平台 确实有这么回事，Mark一下~详情如下：

---
<!-- more -->
## **正文**

### 一、系统设定默认语言规则

1.  系统预置语言，即在 makefile 文件中定义的语言；

	位置：`mediatek/config/${Project}/ProjectConfig.mk`，默认第一个是系统默认语言；

2.  刷机过后，开机重启，如果未插卡，系统语言为预置的语言；

3.  插入SIM卡过后，系统语言根据 SIM 卡来改变， 默认会把刷机过后第一次插的 SIM 卡的语言设置为默认语言；(如果sim的mcc可以在内置的mcc表中找到)；

4.  如果用户没有手动在 Settings 里面设置语言，以后系统语言一直是默认语言（第一次插的 SIM 卡的语言），即使是插入其它国家的卡，系统语言也不会变；

5.  如果用户在 Settings 里面手动设置了语言，以后系统语言会为设置后的语言，无论插入什么卡，系统语言不会变，一直为用户手动设置后的语言。
<font color=red size=3>MTK默认设计是如果手机有插卡，那么在开机是会读取SIM的imsi取出mcc，然后通过mcc来查找对应的language，并设置为手机默认语言</font>
**(相关文件MccTable.javaframeworks\opt\telephony\src\java\com\android\internal\telephony).**

### 二、修改默认语言

1、 如果想手机默认语言不随SIM的MCC走，请按照如下修改：

在**setLocaleFromMccIfNeeded()**函数中

```
//String language = MccTable.defaultLanguageForMcc(mcc);      //注释掉此语句
```

 对于**KK**版本,在**updateMccMncConfiguration()**函数中
 

```
 // locale = getLocaleFromMcc(context, mcc);         //注释掉此语句
```
 2、 如果手机默认语言随SIM卡走

   此为operator CT的定制feature，如果有开OP03 option,默认就支持该功能。

   如果想非OP03也支持该功能，请按照如下修改:

```
   alps/mediatek/frameworks/base/op/java/com/mediatek/op/telephony/TelephonyExt.java中, 使方法isSetLanguangBySIM()返回true，那么手机的默认语言会使用SIM中的EF_LI(6F05)或者EF_EPL(2F05)中的语言。这两个文件优先使用EF_LI中的，如果EF_LI没有匹配的在使用EF_EPL的。
```

**如何打开一种新语言？**

**如何设置开机默认语言?**

3、取消sim卡语言自适应

```
public static void updateMccMncConfiguration(Context context, String mccmnc)
MccTable.java(frameworks\base\telephony\java\com\Android\internal\telephony)
if (mcc != 0) {
                setTimezoneFromMccIfNeeded(context, mcc);
                setLocaleFromMccIfNeeded(context, mcc);
                setWifiCountryCodeFromMcc(context, mcc);
            }
```

每次插卡开机，当sim准备好之后，系统会走`updateMccMncConfiguration`这个函数，其调用这个setLocaleFromMccIfNeeded函数去根据mcc更新系统语言，可以设置是否<font color=red>调用这个函数来决定是否根据sim切换语言。</font>

4、要求每次均可以根据sim卡切换语言

```
public static void setSystemLocale(Context context, String language, String country)
……
config.userSetLocale = false;
config.simSetLocale = true;
……
 am.updateConfiguration(config);
```

第一次开机插入sim卡，如果根据mcc找到相应语言，系统会把当前sim卡语言信息保存起来，以后系统无论插入任何语言的sim卡，系统语言均不会在根据sim在进行切换，其中系统是根据<font color=red>simSetLocale这个参数来决定是否要保存这个信息的，如果在上面把其设为false，则可以实现每次插卡均可自适应(除手动设置语言外)。</font>

这是从网上了解到的资料，讲道理，这并不是我的问题，~~~~(>_<)~~~~，设计如此~



------

### 补充：
**没试过。。** %>_<%


FAQ06189]插国外SIM卡，第一次开机语言没有自适应

问题描述：在有些国家进行场测时，插该国SIM卡（比如俄罗斯），首次开机语言自适应结果是英文，应该变成俄语。而使用中国SIM卡测试该功能时正常的。
 
问题原因：语言自适应需要通过利用SIM卡MCC信息查表来实现，而此表内容对于每个国家并不一样，有些国家没有默认语言，只有较少国家有默认语言。
 
解决方案：补充该表，使每个国家都有默认语言。
 

```
文件：frameworks\base\telephony\java\com\android\internal\telephony\MccTable.java
代码：table = new ArrayList<MccEntry>(240)
修改对应的代码为如下代码：
table.add(new MccEntry(202,"gr",2,"el"));//Greece
table.add(new MccEntry(204,"nl",2,"nl"));//Netherlands (Kingdom of the)
table.add(new MccEntry(206,"be",2,"nl"));//Belgium  
table.add(new MccEntry(208,"fr",2,"fr"));//France
table.add(new MccEntry(212,"mc",2,"fr"));//Monaco (Principality of)
table.add(new MccEntry(213,"ad",2,"en"));//Andorra (Principality of)
table.add(new MccEntry(214,"es",2,"es"));//Spain
table.add(new MccEntry(216,"hu",2,"hu"));//Hungary (Republic of)
table.add(new MccEntry(218,"ba",2,"sl"));//Bosnia and Herzegovina
table.add(new MccEntry(219,"hr",2,"sl"));//Croatia (Republic of)
table.add(new MccEntry(220,"rs",2,"ro"));//Serbia and Montenegro
table.add(new MccEntry(222,"it",2,"it"));//Italy
table.add(new MccEntry(225,"va",2,"it"));//Vatican City State
table.add(new MccEntry(226,"ro",2,"ro"));//Romania
table.add(new MccEntry(228,"ch",2,"de"));//Switzerland (Confederation of)
table.add(new MccEntry(230,"cz",2,"cs"));//Czech Republic
table.add(new MccEntry(231,"sk",2,"cs"));//Slovak Republic
table.add(new MccEntry(232,"at",2,"de"));//Austria
table.add(new MccEntry(234,"gb",2,"en"));//United Kingdom of Great Britain and Northern Ireland
table.add(new MccEntry(235,"gb",2,"en"));//United Kingdom of Great Britain and Northern Ireland
table.add(new MccEntry(238,"dk",2,"da"));//Denmark
table.add(new MccEntry(240,"se",2,"sv"));//Sweden
table.add(new MccEntry(242,"no",2,"no"));//Norway
table.add(new MccEntry(244,"fi",2,"fi"));//Finland
table.add(new MccEntry(246,"lt",2,"lt"));//Lithuania (Republic of)
table.add(new MccEntry(247,"lv",2,"lv"));//Latvia (Republic of)
table.add(new MccEntry(248,"ee",2,"et"));//Estonia (Republic of)
table.add(new MccEntry(250,"ru",2,"ru"));//Russian Federation
table.add(new MccEntry(255,"ua",2,"ru"));//Ukraine
table.add(new MccEntry(257,"by",2,"ru"));//Belarus (Republic of)
table.add(new MccEntry(259,"md",2,"ru"));//Moldova (Republic of)
table.add(new MccEntry(260,"pl",2,"pl"));//Poland (Republic of)
table.add(new MccEntry(262,"de",2,"de"));//Germany (Federal Republic of)
table.add(new MccEntry(266,"gi",2,"en"));//Gibraltar
table.add(new MccEntry(268,"pt",2,"pt"));//Portugal
table.add(new MccEntry(270,"lu",2,"it"));//Luxembourg
table.add(new MccEntry(272,"ie",2,"en"));//Ireland
table.add(new MccEntry(274,"is",2,"is"));//Iceland
table.add(new MccEntry(276,"al",2,"sq"));//Albania (Republic of)
table.add(new MccEntry(278,"mt",2,"mt"));//Malta
table.add(new MccEntry(280,"cy",2,"el"));//Cyprus (Republic of)
table.add(new MccEntry(282,"ge",2,"ka"));//Georgia
table.add(new MccEntry(283,"am",2,"ru"));//Armenia (Republic of)
table.add(new MccEntry(284,"bg",2,"bg"));//Bulgaria (Republic of)
table.add(new MccEntry(286,"tr",2,"tr"));//Turkey
table.add(new MccEntry(288,"fo",2,"fo"));//Faroe Islands
table.add(new MccEntry(289,"ge",2,"ka"));//Abkhazia (Georgia)
table.add(new MccEntry(290,"gl",2,"kl"));//Greenland (Denmark)
table.add(new MccEntry(292,"sm",2,"it"));//San Marino (Republic of)
table.add(new MccEntry(293,"si",2,"sl"));//Slovenia (Republic of)
table.add(new MccEntry(294,"mk",2,"sh"));//The Former Yugoslav Republic of Macedonia
table.add(new MccEntry(295,"li",2,"de"));//Liechtenstein (Principality of)
table.add(new MccEntry(297,"me",2,"ru"));//Montenegro (Republic of)
table.add(new MccEntry(302,"ca",3,"fr"));//Canada
table.add(new MccEntry(308,"pm",2,"fr"));//Saint Pierre and Miquelon (Collectivit territoriale de la Rpublique franaise)
table.add(new MccEntry(310,"us",3,"en"));//United States of America
table.add(new MccEntry(311,"us",3,"en"));//United States of America
table.add(new MccEntry(312,"us",3,"en"));//United States of America
table.add(new MccEntry(313,"us",3,"en"));//United States of America
table.add(new MccEntry(314,"us",3,"en"));//United States of America
table.add(new MccEntry(315,"us",3,"en"));//United States of America
table.add(new MccEntry(316,"us",3,"en"));//United States of America
table.add(new MccEntry(330,"pr",2,"en"));//Puerto Rico
table.add(new MccEntry(332,"vi",2,"en"));//United States Virgin Islands
table.add(new MccEntry(334,"mx",3,"es"));//Mexico
table.add(new MccEntry(338,"jm",3,"en"));//Jamaica
table.add(new MccEntry(340,"gp",2,"fr"));//Guadeloupe (French Department of)
table.add(new MccEntry(342,"bb",3,"en"));//Barbados
table.add(new MccEntry(344,"ag",3,"en"));//Antigua and Barbuda
table.add(new MccEntry(346,"ky",3,"en"));//Cayman Islands
table.add(new MccEntry(348,"vg",3,"en"));//British Virgin Islands
table.add(new MccEntry(350,"bm",2,"en"));//Bermuda
table.add(new MccEntry(352,"gd",2,"en"));//Grenada
table.add(new MccEntry(354,"ms",2,"en"));//Montserrat
table.add(new MccEntry(356,"kn",2,"en"));//Saint Kitts and Nevis
table.add(new MccEntry(358,"lc",2,"en"));//Saint Lucia
table.add(new MccEntry(360,"vc",2,"en"));//Saint Vincent and the Grenadines
table.add(new MccEntry(362,"nl",2,"nl"));//Netherlands Antilles
table.add(new MccEntry(363,"aw",2,"en"));//Aruba
table.add(new MccEntry(364,"bs",2,"en"));//Bahamas (Commonwealth of the)
table.add(new MccEntry(365,"ai",3,"en"));//Anguilla
table.add(new MccEntry(366,"dm",2,"en"));//Dominica (Commonwealth of)
table.add(new MccEntry(368,"cu",2,"es"));//Cuba
table.add(new MccEntry(370,"do",2,"es"));//Dominican Republic
table.add(new MccEntry(372,"ht",2,"fr"));//Haiti (Republic of)
table.add(new MccEntry(374,"tt",2,"en"));//Trinidad and Tobago
table.add(new MccEntry(376,"tc",2,"en"));//Turks and Caicos Islands
table.add(new MccEntry(400,"az",2,"ru"));//Azerbaijani Republic
table.add(new MccEntry(401,"kz",2,"ru"));//Kazakhstan (Republic of)
table.add(new MccEntry(402,"bt",2,"ts"));//Bhutan (Kingdom of)
table.add(new MccEntry(404,"in",2,"hi"));//India (Republic of)
table.add(new MccEntry(405,"in",2,"hi"));//India (Republic of)
table.add(new MccEntry(410,"pk",2,"ur"));//Pakistan (Islamic Republic of)
table.add(new MccEntry(412,"af",2,"en"));//Afghanistan
table.add(new MccEntry(413,"lk",2,"si"));//Sri Lanka (Democratic Socialist Republic of)
table.add(new MccEntry(414,"mm",2,"my"));//Myanmar (Union of)
table.add(new MccEntry(415,"lb",2,"ar"));//Lebanon
table.add(new MccEntry(416,"jo",2,"ar"));//Jordan (Hashemite Kingdom of)
table.add(new MccEntry(417,"sy",2,"ar"));//Syrian Arab Republic
table.add(new MccEntry(418,"iq",2,"ar"));//Iraq (Republic of)
table.add(new MccEntry(419,"kw",2,"ar"));//Kuwait (State of)
table.add(new MccEntry(420,"sa",2,"ar"));//Saudi Arabia (Kingdom of)
table.add(new MccEntry(421,"ye",2,"ar"));//Yemen (Republic of)
table.add(new MccEntry(422,"om",2,"ar"));//Oman (Sultanate of)
table.add(new MccEntry(423,"ps",2,"ar"));//Palestine
table.add(new MccEntry(424,"ae",2,"en"));//United Arab Emirates
table.add(new MccEntry(425,"il",2,"iw"));//Israel (State of)
table.add(new MccEntry(426,"bh",2,"ar"));//Bahrain (Kingdom of)
table.add(new MccEntry(427,"qa",2,"ar"));//Qatar (State of)
table.add(new MccEntry(428,"mn",2,"mn"));//Mongolia
table.add(new MccEntry(429,"np",2,"ne"));//Nepal
table.add(new MccEntry(430,"ae",2,"en"));//United Arab Emirates
table.add(new MccEntry(431,"ae",2,"en"));//United Arab Emirates
table.add(new MccEntry(432,"ir",2,"fa"));//Iran (Islamic Republic of)
table.add(new MccEntry(434,"uz",2,"ru"));//Uzbekistan (Republic of)
table.add(new MccEntry(436,"tj",2,"ru"));//Tajikistan (Republic of)
table.add(new MccEntry(437,"kg",2,"ru"));//Kyrgyz Republic
table.add(new MccEntry(438,"tm",2,"ru"));//Turkmenistan
table.add(new MccEntry(440,"jp",2,"ja"));//Japan
table.add(new MccEntry(441,"jp",2,"ja"));//Japan
table.add(new MccEntry(450,"kr",2,"ko"));//Korea (Republic of)
table.add(new MccEntry(452,"vn",2,"vi"));//Viet Nam (Socialist Republic of)
table.add(new MccEntry(454,"hk",2,"en"));//Hong Kong, China
table.add(new MccEntry(455,"mo",2,"mn"));//Macao, China
table.add(new MccEntry(456,"kh",2,"km"));//Cambodia (Kingdom of)
table.add(new MccEntry(457,"la",2,"lo"));//Lao People's Democratic Republic
table.add(new MccEntry(460,"cn",2,"zh"));//China (People's Republic of)
table.add(new MccEntry(461,"cn",2,"zh"));//China (People's Republic of)
table.add(new MccEntry(466,"tw",2,"zh"));//Taiwan, China
table.add(new MccEntry(467,"kp",2,"ko"));//Democratic People's Republic of Korea
table.add(new MccEntry(470,"bd",2,"bn"));//Bangladesh (People's Republic of)
table.add(new MccEntry(472,"mv",2,"en"));//Maldives (Republic of)
table.add(new MccEntry(502,"my",2,"ms"));//Malaysia
table.add(new MccEntry(505,"au",2,"en"));//Australia
table.add(new MccEntry(510,"id",2,"in"));//Indonesia (Republic of)
table.add(new MccEntry(514,"tl",2,"pt"));//Democratic Republic of Timor-Leste
table.add(new MccEntry(515,"ph",2,"en"));//Philippines (Republic of the)
table.add(new MccEntry(520,"th",2,"th"));//Thailand
table.add(new MccEntry(525,"sg",2,"en"));//Singapore (Republic of)
table.add(new MccEntry(528,"bn",2,"ms"));//Brunei Darussalam
table.add(new MccEntry(530,"nz",2,"en"));//New Zealand
table.add(new MccEntry(534,"mp",2,"en"));//Northern Mariana Islands (Commonwealth of the)
table.add(new MccEntry(535,"gu",2,"en"));//Guam
table.add(new MccEntry(536,"nr",2,"na"));//Nauru (Republic of)
table.add(new MccEntry(537,"pg",2,"en"));//Papua New Guinea
table.add(new MccEntry(539,"to",2,"to"));//Tonga (Kingdom of)
table.add(new MccEntry(540,"sb",2,"en"));//Solomon Islands
table.add(new MccEntry(541,"vu",2,"en"));//Vanuatu (Republic of)
table.add(new MccEntry(542,"fj",2,"en"));//Fiji (Republic of)
table.add(new MccEntry(543,"wf",2,"fr"));//Wallis and Futuna (Territoire franais d'outre-mer)
table.add(new MccEntry(544,"as",2,"en"));//American Samoa
table.add(new MccEntry(545,"ki",2,"en"));//Kiribati (Republic of)
table.add(new MccEntry(546,"nc",2,"fr"));//New Caledonia (Territoire franais d'outre-mer)
table.add(new MccEntry(547,"pf",2,"fr"));//French Polynesia (Territoire franais d'outre-mer)
table.add(new MccEntry(548,"ck",2,"mi"));//Cook Islands
table.add(new MccEntry(549,"ws",2,"sm"));//Samoa (Independent State of)
table.add(new MccEntry(550,"fm",2,"en"));//Micronesia (Federated States of)
table.add(new MccEntry(551,"mh",2,"en"));//Marshall Islands (Republic of the)
table.add(new MccEntry(552,"pw",2,"en"));//Palau (Republic of)
table.add(new MccEntry(602,"eg",2,"ar"));//Egypt (Arab Republic of)
table.add(new MccEntry(603,"dz",2,"ar"));//Algeria (People's Democratic Republic of)
table.add(new MccEntry(604,"ma",2,"ar"));//Morocco (Kingdom of)
table.add(new MccEntry(605,"tn",2,"ar"));//Tunisia
table.add(new MccEntry(606,"ly",2,"ar"));//Libya (Socialist People's Libyan Arab Jamahiriya)
table.add(new MccEntry(607,"gm",2,"en"));//Gambia (Republic of the)
table.add(new MccEntry(608,"sn",2,"fr"));//Senegal (Republic of)
table.add(new MccEntry(609,"mr",2,"ar"));//Mauritania (Islamic Republic of)
table.add(new MccEntry(610,"ml",2,"fr"));//Mali (Republic of)
table.add(new MccEntry(611,"gn",2,"fr"));//Guinea (Republic of)
table.add(new MccEntry(612,"ci",2,"en"));//Cte d'Ivoire (Republic of)
table.add(new MccEntry(613,"bf",2,"fr"));//Burkina Faso
table.add(new MccEntry(614,"ne",2,"fr"));//Niger (Republic of the)
table.add(new MccEntry(615,"tg",2,"fr"));//Togolese Republic
table.add(new MccEntry(616,"bj",2,"fr"));//Benin (Republic of)
table.add(new MccEntry(617,"mu",2,"en"));//Mauritius (Republic of)
table.add(new MccEntry(618,"lr",2,"en"));//Liberia (Republic of)
table.add(new MccEntry(619,"sl",2,"en"));//Sierra Leone
table.add(new MccEntry(620,"gh",2,"en"));//Ghana
table.add(new MccEntry(621,"ng",2,"en"));//Nigeria (Federal Republic of)
table.add(new MccEntry(622,"td",2,"fr"));//Chad (Republic of)
table.add(new MccEntry(623,"cf",2,"fr"));//Central African Republic
table.add(new MccEntry(624,"cm",2,"fr"));//Cameroon (Republic of)
table.add(new MccEntry(625,"cv",2,"pt"));//Cape Verde (Republic of)
table.add(new MccEntry(626,"st",2,"pt"));//Sao Tome and Principe (Democratic Republic of)
table.add(new MccEntry(627,"gq",2,"es"));//Equatorial Guinea (Republic of)
table.add(new MccEntry(628,"ga",2,"fr"));//Gabonese Republic
table.add(new MccEntry(629,"cg",2,"fr"));//Congo (Republic of the)
table.add(new MccEntry(630,"cg",2,"fr"));//Democratic Republic of the Congo
table.add(new MccEntry(631,"ao",2,"pt"));//Angola (Republic of)
table.add(new MccEntry(632,"gw",2,"pt"));//Guinea-Bissau (Republic of)
table.add(new MccEntry(633,"sc",2,"fr"));//Seychelles (Republic of)
table.add(new MccEntry(634,"sd",2,"ar"));//Sudan (Republic of the)
table.add(new MccEntry(635,"rw",2,"fr"));//Rwanda (Republic of)
table.add(new MccEntry(636,"et",2,"am"));//Ethiopia (Federal Democratic Republic of)
table.add(new MccEntry(637,"so",2,"so"));//Somali Democratic Republic
table.add(new MccEntry(638,"dj",2,"fr"));//Djibouti (Republic of)
table.add(new MccEntry(639,"ke",2,"sw"));//Kenya (Republic of)
table.add(new MccEntry(640,"tz",2,"en"));//Tanzania (United Republic of)
table.add(new MccEntry(641,"ug",2,"en"));//Uganda (Republic of)
table.add(new MccEntry(642,"bi",2,"fr"));//Burundi (Republic of)
table.add(new MccEntry(643,"mz",2,"pt"));//Mozambique (Republic of)
table.add(new MccEntry(645,"zm",2,"en"));//Zambia (Republic of)
table.add(new MccEntry(646,"mg",2,"fr"));//Madagascar (Republic of)
table.add(new MccEntry(647,"re",2,"fr"));//Reunion (French Department of)
table.add(new MccEntry(648,"zw",2,"en"));//Zimbabwe (Republic of)
table.add(new MccEntry(649,"na",2,"en"));//Namibia (Republic of)
table.add(new MccEntry(650,"mw",2,"en"));//Malawi
table.add(new MccEntry(651,"ls",2,"en"));//Lesotho (Kingdom of)
table.add(new MccEntry(652,"bw",2,"en"));//Botswana (Republic of)
table.add(new MccEntry(653,"sz",2,"en"));//Swaziland (Kingdom of)
table.add(new MccEntry(654,"km",2,"fr"));//Comoros (Union of the)
table.add(new MccEntry(655,"za",2,"en"));//South Africa (Republic of)
table.add(new MccEntry(657,"er",2,"ar"));//Eritrea
table.add(new MccEntry(702,"bz",2,"en"));//Belize
table.add(new MccEntry(704,"gt",2,"es"));//Guatemala (Republic of)
table.add(new MccEntry(706,"sv",2,"es"));//El Salvador (Republic of)
table.add(new MccEntry(708,"hn",3,"es"));//Honduras (Republic of)
table.add(new MccEntry(710,"ni",2,"es"));//Nicaragua
table.add(new MccEntry(712,"cr",2,"es"));//Costa Rica
table.add(new MccEntry(714,"pa",2,"es"));//Panama (Republic of)
table.add(new MccEntry(716,"pe",2,"es"));//Peru
table.add(new MccEntry(722,"ar",3,"es"));//Argentine Republic
table.add(new MccEntry(724,"br",2,"pt"));//Brazil (Federative Republic of)
table.add(new MccEntry(730,"cl",2,"es"));//Chile
table.add(new MccEntry(732,"co",3,"es"));//Colombia (Republic of)
table.add(new MccEntry(734,"ve",2,"es"));//Venezuela (Bolivarian Republic of)
table.add(new MccEntry(736,"bo",2,"es"));//Bolivia (Republic of)
table.add(new MccEntry(738,"gy",2,"en"));//Guyana
table.add(new MccEntry(740,"ec",2,"es"));//Ecuador
table.add(new MccEntry(742,"gf",2,"fr"));//French Guiana (French Department of)
table.add(new MccEntry(744,"py",2,"es"));//Paraguay (Republic of)
table.add(new MccEntry(746,"sr",2,"nl"));//Suriname (Republic of)
table.add(new MccEntry(748,"uy",2,"es"));//Uruguay (Eastern Republic of)
table.add(new MccEntry(750,"fk",2,"en"));//Falkland Islands (Malvinas)
```


