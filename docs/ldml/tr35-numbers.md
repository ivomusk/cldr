## Unicode Technical Standard #35

# Unicode Locale Data Markup Language (LDML)<br/>Part 3: Numbers

|Version|48 (draft)|
|-------|----------|
|Editors|Shane F. Carr (<a href="mailto:shane@unicode.org">shane@unicode.org</a>) and <a href="tr35.md#Acknowledgments">other CLDR committee members|

For the full header, summary, and status, see [Part 1: Core](tr35.md).

### _Summary_

This document describes parts of an XML format (_vocabulary_) for the exchange of structured locale data. This format is used in the [Unicode Common Locale Data Repository](https://www.unicode.org/cldr/).

This is a partial document, describing only those parts of the LDML that are relevant for number and currency formatting. For the other parts of the LDML see the [main LDML document](tr35.md) and the links above.

### _Status_

_This is a draft document which may be updated, replaced, or superseded by other documents at any time.
Publication does not imply endorsement by the Unicode Consortium.
This is not a stable document; it is inappropriate to cite this document as other than a work in progress._

<!-- _This document has been reviewed by Unicode members and other interested parties, and has been approved for publication by the Unicode Consortium.
This is a stable document and may be used as reference material or cited as a normative reference by other specifications._ -->

> _**A Unicode Technical Standard (UTS)** is an independent specification. Conformance to the Unicode Standard does not imply conformance to any UTS._

_Please submit corrigenda and other comments with the CLDR bug reporting form [[Bugs](https://cldr.unicode.org/index/bug-reports)].
Related information that is useful in understanding this document is found in the [References](#References).
For the latest version of the Unicode Standard see [[Unicode](https://www.unicode.org/versions/latest/)].
For more information see [About Unicode Technical Reports](https://www.unicode.org/reports/about-reports.html) and the [Specifications FAQ](https://www.unicode.org/faq/specifications.html).
Unicode Technical Reports are governed by the Unicode [Terms of Use](https://www.unicode.org/copyright.html)._

## <a name="Parts" href="#Parts">Parts</a>

The LDML specification is divided into the following parts:

*   Part 1: [Core](tr35.md#Contents) (languages, locales, basic structure)
*   Part 2: [General](tr35-general.md#Contents) (display names & transforms, etc.)
*   Part 3: [Numbers](tr35-numbers.md#Contents) (number & currency formatting)
*   Part 4: [Dates](tr35-dates.md#Contents) (date, time, time zone formatting)
*   Part 5: [Collation](tr35-collation.md#Contents) (sorting, searching, grouping)
*   Part 6: [Supplemental](tr35-info.md#Contents) (supplemental data)
*   Part 7: [Keyboards](tr35-keyboards.md#Contents) (keyboard mappings)
*   Part 8: [Person Names](tr35-personNames.md#Contents) (person names)
*   Part 9: [MessageFormat](tr35-messageFormat.md#Contents) (message format)

## <a name="Contents" href="#Contents">Contents of Part 3, Numbers</a>

* [Numbering Systems](#Numbering_Systems)
* [Number Elements](#Number_Elements)
  * [Default Numbering System](#defaultNumberingSystem)
  * [Other Numbering Systems](#otherNumberingSystems)
  * [Number Symbols](#Number_Symbols)
  * [Number Formats](#Number_Formats)
    * [Compact Number Formats](#Compact_Number_Formats)
    * [Currency Formats](#Currency_Formats)
  * [Miscellaneous Patterns](#Miscellaneous_Patterns)
  * [Minimal Pairs](#Minimal_Pairs)
* [Number Format Patterns](#Number_Format_Patterns)
  * [Number Patterns](#Number_Patterns)
    * Table: [Number Pattern Examples](#Number_Pattern_Examples)
  * [Special Pattern Characters](#Special_Pattern_Characters)
    * Table: [Number Pattern Character Definitions](#Number_Pattern_Character_Definitions)
    * Table: [Sample Patterns and Results](#Sample_Patterns_and_Results)
    * [Explicit Plus Signs](#Explicit_Plus)
  * [Formatting](#Formatting)
  * [Scientific Notation](#sci)
  * [Significant Digits](#sigdig)
    * Table: [Significant Digits Examples](#Significant_Digits_Examples)
  * [Padding](#Padding)
  * [Rounding](#Rounding)
  * [Quoting Rules](#Quoting_Rules)
* [Currencies](#Currencies)
  * [Supplemental Currency Data](#Supplemental_Currency_Data)
* [Language Plural Rules](#Language_Plural_Rules)
  * [Explicit 0 and 1 rules](#Explicit_0_1_rules)
  * [Plural rules syntax](#Plural_rules_syntax)
    * [Operands](#Operands)
      * Table: [Plural Operand Meanings](#Plural_Operand_Meanings)
      * Table: [Plural Operand Examples](#Plural_Operand_Examples)
    * [Relations](#Relations)
      * Table: [Relations Examples](#Relations_Examples)
      * Table: [Plural Rules Examples](#Plural_Rules_Examples)
    * [Samples](#Samples)
      * Table: [Plural Samples Examples](#Plural_Samples_Examples)
    * [Using Cardinals](#Using_cardinals)
  * [Plural Ranges](#Plural_Ranges)
* [Rule-Based Number Formatting](#Rule-Based_Number_Formatting)
* [Parsing Numbers](#Parsing_Numbers)
* [Number Range Formatting](#Number_Range_Formatting)
  * [Approximate Number Formatting](#Approximate_Number_Formatting)
  * [Collapsing Number Ranges](#Collapsing_Number_Ranges)
  * [Range Pattern Processing](#Range_Pattern_Processing)

## <a name="Numbering_Systems" href="#Numbering_Systems">Numbering Systems</a>

```dtd
<!ELEMENT numberingSystems ( numberingSystem* ) >
<!ELEMENT numberingSystem EMPTY >
<!ATTLIST numberingSystem id NMTOKEN #REQUIRED >
<!ATTLIST numberingSystem type ( numeric | algorithmic ) #REQUIRED >
<!ATTLIST numberingSystem radix NMTOKEN #IMPLIED >
<!ATTLIST numberingSystem digits CDATA #IMPLIED >
<!ATTLIST numberingSystem rules CDATA #IMPLIED >
```

Numbering systems information is used to define different representations for numeric values to an end user. Numbering systems are defined in CLDR as one of two different types: algorithmic and numeric. Numeric systems are simply a decimal based system that uses a predefined set of digits to represent numbers. Examples are Western digits (ASCII digits), Thai digits, Devanagari digits. Algorithmic systems are more complex in nature, since the proper formatting and presentation of a numeric quantity is based on some algorithm or set of rules. Examples are Chinese numerals, Hebrew numerals, or Roman numerals. In CLDR, the rules for presentation of numbers in an algorithmic system are defined using the RBNF syntax described in _[Section 6: Rule-Based Number Formatting](#Rule-Based_Number_Formatting)_.

Attributes for the `<numberingSystem>` element are as follows:

- `id` - Specifies the name of the numbering system that can be used to designate its use in formatting.
- `type` - Specifies whether the numbering system is algorithmic or numeric.
- `digits` - For numeric systems, specifies the digits used to represent numbers, in order, starting from zero.
- `rules` - Specifies the RBNF ruleset to be used for formatting numbers from this numbering system. The rules specifier can contain simply a ruleset name, in which case the ruleset is assumed to be found in the rule set grouping "NumberingSystemRules". Alternatively, the specifier can denote a specific locale, ruleset grouping, and ruleset name, separated by slashes.

Examples:

```xml
<!-- ASCII digits - A numeric system -->
<numberingSystem id="latn" type="numeric" digits="0123456789"/>

<!-- A numeric system using Thai digits -->
<numberingSystem id="thai" type="numeric" digits="๐๑๒๓๔๕๖๗๘๙"/>

<!-- An algorithmic system - Georgian numerals, rules found in NumberingSystemRules -->
<numberingSystem id="geor" type="algorithmic" rules="georgian"/>

<!-- An algorithmic system. Traditional Chinese numerals -->
<numberingSystem id="hant" type="algorithmic" rules="zh_Hant/SpelloutRules/spellout-cardinal"/>
```

For general information about the numbering system data, including the BCP47 identifiers, see the main document _Section Q.1.1 [Numbering System Data](tr35.md#Numbering%20System%20Data)._

## <a name="Number_Elements" href="#Number_Elements">Number Elements</a>

```dtd
<!ELEMENT numbers ( alias | ( defaultNumberingSystem*, otherNumberingSystems*, minimumGroupingDigits*, symbols*, decimalFormats*, scientificFormats*, percentFormats*, currencyFormats*, currencies?, miscPatterns*, minimalPairs*, special* ) ) >
```

The numbers element supplies information for formatting and parsing numbers and currencies. It has the following sub-elements: `<defaultNumberingSystem>`, `<otherNumberingSystems>`, `<symbols>`, `<decimalFormats>`, `<scientificFormats>`, `<percentFormats>`, `<currencyFormats>`, and `<currencies>`. The currency IDs are from [[ISO4217](tr35.md#ISO4217)] (plus some additional common-use codes). For more information, including the pattern structure, see _[Section 3: Number Format Patterns](#Number_Format_Patterns)_.

### <a name="defaultNumberingSystem" href="#defaultNumberingSystem">Default Numbering System</a>

```dtd
<!ELEMENT defaultNumberingSystem ( #PCDATA )>
```

This element indicates which numbering system should be used for presentation of numeric quantities in the given locale.

### <a name="otherNumberingSystems" href="#otherNumberingSystems">Other Numbering Systems</a>

```dtd
<!ELEMENT otherNumberingSystems ( alias | ( native*, traditional*, finance*)) >
```

This element defines general categories of numbering systems that are sometimes used in the given locale for formatting numeric quantities. These additional numbering systems are often used in very specific contexts, such as in calendars or for financial purposes. There are currently three defined categories, as follows:

**native**

> Defines the numbering system used for the native digits, usually defined as a part of the script used to write the language. The native numbering system can only be a numeric positional decimal-digit numbering system, using digits with General_Category=Decimal_Number. Note: In locales where the native numbering system is the default, it is assumed that the numbering system "latn" (Western digits 0-9) is always acceptable, and can be selected using the -nu keyword as part of a Unicode locale identifier.

**traditional**

> Defines the traditional numerals for a locale. This numbering system may be numeric or algorithmic. If the traditional numbering system is not defined, applications should use the native numbering system as a fallback.

**finance**

> Defines the numbering system used for financial quantities. This numbering system may be numeric or algorithmic. This is often used for ideographic languages such as Chinese, where it would be easy to alter an amount represented in the default numbering system simply by adding additional strokes. If the financial numbering system is not specified, applications should use the default numbering system as a fallback.

The categories defined for other numbering systems can be used in a Unicode locale identifier to select the proper numbering system without having to know the specific numbering system by name. For example:

*   To select Hindi language using the native digits for numeric formatting, use locale ID: "hi-IN-u-nu-native".
*   To select Chinese language using the appropriate financial numerals, use locale ID: "zh-u-nu-finance".
*   To select Tamil language using the traditional Tamil numerals, use locale ID: "ta-u-nu-traditio".
*   To select Arabic language using western digits 0-9, use locale ID: "ar-u-nu-latn".

For more information on numbering systems and their definitions, see _[Section 1: Numbering Systems](#Numbering_Systems)_.

### <a name="Number_Symbols" href="#Number_Symbols">Number Symbols</a>

```dtd
<!ELEMENT symbols (alias | (decimal*, group*, list*, percentSign*, nativeZeroDigit*, patternDigit*, plusSign*, minusSign*, approximatelySign*, exponential*, superscriptingExponent*, perMille*, infinity*, nan*, currencyDecimal*, currencyGroup*, timeSeparator*, special*)) >
```

Number symbols define the localized symbols that are commonly used when formatting numbers in a given locale. These symbols can be referenced using a number formatting pattern as defined in _[Section 3: Number Format Patterns](#Number_Format_Patterns)_.

The available number symbols are as follows:

**decimal**

> separates the integer and fractional part of the number.

**group**

> separates clusters of integer digits to make large numbers more legible; commonly used for thousands (grouping size 3, e.g. "100,000,000") or in some locales, ten-thousands (grouping size 4, e.g. "1,0000,0000"). There may be two different grouping sizes: The _primary grouping size_ used for the least significant integer group, and the _secondary grouping size_ used for more significant groups; these are not the same in all locales (e.g. "12,34,56,789"). If a pattern contains multiple grouping separators, the interval between the last one and the end of the integer defines the primary grouping size, and the interval between the last two defines the secondary grouping size. All others are ignored, so "#,##,###,####" == "###,###,####" == "##,#,###,####".

**list**

> symbol used to separate numbers in a list intended to represent structured data such as an array; must be different from the **decimal** value. This list separator is for “non-linguistic” usage as opposed to the listPatterns for “linguistic” lists (e.g. “Bob, Carol, and Ted”) described in Part 2, _[List Patterns](tr35-general.md#ListPatterns)_.

**percentSign**

> symbol used to indicate a percentage (1/100th) amount. (If present, the value is also multiplied by 100 before formatting. That way 1.23 → 123%)

~~**nativeZeroDigit**~~

> Deprecated - do not use.

~~**patternDigit**~~

> Deprecated. This was formerly used to provide the localized pattern character corresponding to '#', but localization of the pattern characters themselves has been deprecated for some time (determining the locale-specific _replacements_ for pattern characters is of course not deprecated and is part of normal number formatting).

**minusSign**

> Symbol used to denote negative value.

**plusSign**

> Symbol used to denote positive value.  It can be used to produce modified patterns, so that 3.12 is formatted as "+3.12", for example. The standard number patterns (except for type="accounting") will contain the minusSign, explicitly or implicitly. In the explicit pattern, the value of the plusSign can be substituted for the value of the minusSign to produce a pattern that has an explicit plus sign.

**approximatelySign**

> Symbol used to denote a value that is approximate but not exact. The symbol is substituted in place of the minusSign using the same semantics as plusSign substitution.

**exponential**

> Symbol separating the mantissa and exponent values.

**superscriptingExponent**

> (Programmers are used to the fallback exponent style “1.23E4”, but that should not be shown to end-users. Instead, the exponential notation superscriptingExponent should be used to show a format like “1.23 × 10<sup>4</sup>”. ) The superscripting can use markup, such as `<sup>4</sup>` in HTML, or for the special case of Latin digits, use the superscript characters: U+207B ( ⁻ ), U+2070 ( ⁰ ), U+00B9 ( ¹ ), U+00B2 ( ² ), U+00B3 ( ³ ), U+2074 ( ⁴ ) .. U+2079 ( ⁹ ).

**perMille**

> symbol used to indicate a per-mille (1/1000th) amount. (If present, the value is also multiplied by 1000 before formatting. That way 1.23 → 1230 [1/000])

**infinity**

> The infinity sign. Corresponds to the IEEE infinity bit pattern.

**nan - Not a number**

> The NaN sign. Corresponds to the IEEE NaN bit pattern.

**currencyDecimal**

> Optional. If specified, then for currency formatting/parsing this is used as the decimal separator instead of using the regular decimal separator; otherwise, the regular decimal separator is used.

**currencyGroup**

> Optional. If specified, then for currency formatting/parsing this is used as the group separator instead of using the regular group separator; otherwise, the regular group separator is used.

**timeSeparator**

> This replaces any use of the timeSeparator pattern character in a date-time format pattern (no timeSeparator pattern character is currently defined, see note below). This allows the same time format to be used for multiple number systems when the time separator depends on the number system. For example, the time format for Arabic should be COLON when using the Latin numbering system (0, 1, 2, …), but when the Arabic numbering system is used (٠‎ - ١‎ - ٢‎ …), the traditional time separator in older print styles was often ARABIC COMMA.
>
> **Note:** In CLDR 26 the timeSeparator pattern character was specified to be COLON. This was withdrawn in CLDR 28 due to backward compatibility issues, and no timeSeparator pattern character is currently defined. No CLDR locales are known to have a need to specify timeSeparator symbols that depend on number system; if this changes in the future a different timeSeparator pattern character will be defined. In the meantime, since CLDR data consumers can still request the timeSeparator symbol, it should match the symbol actually used in the [timeFormats](tr35-dates.md#timeFormats) and [availableFormats](tr35-dates.md#availableFormats_appendItems) items.

Example:

```xml
<symbols numberSystem="latn">
    <decimal>.</decimal>
    <group>,</group>
    <list>;</list>
    <percentSign>%</percentSign>
    <patternDigit>#</patternDigit>
    <plusSign>+</plusSign>
    <minusSign>-</minusSign>
    <approximatelySign>~</approximatelySign>
    <exponential>E</exponential>
    <superscriptingExponent>×</superscriptingExponent>
    <perMille>‰</perMille>
    <infinity>∞</infinity>
    <nan>☹</nan>
    <timeSeparator>:</timeSeparator>
</symbols>
```

```dtd
<!ATTLIST symbols numberSystem CDATA #IMPLIED >
```
The `numberSystem` attribute is used to specify that the given number symbols are to be used when the given numbering system is active. Number symbols can only be defined for numbering systems of the "numeric" type, since any special symbols required for an algorithmic numbering system should be specified by the RBNF formatting rules used for that numbering system. By default, number symbols without a specific `numberSystem` attribute are assumed to be used for the "latn" numbering system, which is western (ASCII) digits; however, number symbols without a specific `numberSystem` attribute should not be used and will be deprecated in CLDR v48. Locales that specify a numbering system other than "latn" as the default should also specify number formatting symbols that are appropriate for use within the context of the given numbering system. For example, a locale that uses the Arabic-Indic digits as its default would likely use an Arabic comma for the grouping separator rather than the ASCII comma.
For more information on numbering systems and their definitions, see _[Section 1: Numbering Systems](#Numbering_Systems)_.

### <a name="Number_Formats" href="#Number_Formats">Number Formats</a>

```dtd
<!ELEMENT decimalFormats (alias | (default*, decimalFormatLength*, special*)) >
<!ELEMENT decimalFormatLength (alias | (default*, decimalFormat*, special*)) >
<!ATTLIST decimalFormatLength type ( full | long | medium | short ) #IMPLIED >
<!ELEMENT decimalFormat (alias | (pattern*, special*)) >
```

(scientificFormats, percentFormats have the same structure)

Number formats are used to define the rules for formatting numeric quantities using the pattern syntax described in _[Section 3: Number Format Patterns](#Number_Format_Patterns)_.

Different formats are provided for different contexts, as follows:

**decimalFormats**

> The normal locale specific way to write a base 10 number. Variations of the decimalFormat pattern are provided that allow compact number formatting.

**percentFormats**

> Pattern for use with percentage formatting

**scientificFormats**

> Pattern for use with scientific (exponent) formatting.

Example:

```xml
<decimalFormats numberSystem="latn">
  <decimalFormatLength type="long">
    <decimalFormat>
      <pattern>#,##0.###</pattern>
    </decimalFormat>
  </decimalFormatLength>
</decimalFormats>

<scientificFormats numberSystem="latn">
  <default type="long"/>
  <scientificFormatLength type="long">
    <scientificFormat>
      <pattern>0.000###E+00</pattern>
    </scientificFormat>
  </scientificFormatLength>
  <scientificFormatLength type="medium">
    <scientificFormat>
      <pattern>0.00##E+00</pattern>
    </scientificFormat>
  </scientificFormatLength>
</scientificFormats>

<percentFormats numberSystem="latn">
  <percentFormatLength type="long">
    <percentFormat>
      <pattern>#,##0%</pattern>
    </percentFormat>
  </percentFormatLength>
</percentFormats>
```

```dtd
<!ATTLIST symbols numberSystem CDATA #IMPLIED >
```

The `numberSystem` attribute is used to specify that the given number formatting pattern(s) are to be used when the given numbering system is active. By default, number formatting patterns without a specific `numberSystem` attribute are assumed to be used for the "latn" numbering system, which is western (ASCII) digits; however, number formatting patterns without a specific `numberSystem` attribute should not be used and will be deprecated in CLDR v48. Locales that specify a numbering system other than "latn" as the default should also specify number formatting patterns that are appropriate for use within the context of the given numbering system.
For more information on numbering systems and their definitions, see _[Section 1: Numbering Systems](#Numbering_Systems)_.

#### <a name="Compact_Number_Formats" href="#Compact_Number_Formats">Compact Number Formats</a>

A pattern `type` attribute is used for _compact number formats_, such as the following:

```xml
<decimalFormatLength type="long">
    <decimalFormat>
        <pattern type="1000" count="one">0 millier</pattern>
        <pattern type="1000" count="other">0 milliers</pattern>
        <pattern type="10000" count="one">00 mille</pattern>
        <pattern type="10000" count="other">00 mille</pattern>
        <pattern type="100000" count="one">000 mille</pattern>
        <pattern type="100000" count="other">000 mille</pattern>
        <pattern type="1000000" count="one">0 million</pattern>
        <pattern type="1000000" count="other">0 millions</pattern>
        …
    </decimalFormat>
</decimalFormatLength>
<decimalFormatLength type="short">
    <decimalFormat>
        <pattern type="1000" count="one">0 K</pattern>
        <pattern type="1000" count="other">0 K</pattern>
        <pattern type="10000" count="one">00 K</pattern>
        <pattern type="10000" count="other">00 K</pattern>
        <pattern type="100000" count="one">000 K</pattern>
        <pattern type="100000" count="other">000 K</pattern>
        <pattern type="1000000" count="one">0 M</pattern>
        <pattern type="1000000" count="other">0 M</pattern>
        …
    </decimalFormat>
</decimalFormatLength>
…
<currencyFormatLength type="short">
    <currencyFormat type="standard">
        <pattern type="1000" count="one">0 K ¤</pattern>
        <pattern type="1000" count="other">0 K ¤</pattern>
        <pattern type="10000" count="one">00 K ¤</pattern>
        <pattern type="10000" count="other">00 K ¤</pattern>
        <pattern type="100000" count="one">000 K ¤</pattern>
        <pattern type="100000" count="other">000 K ¤</pattern>
        <pattern type="1000000" count="one">0 M ¤</pattern>
        <pattern type="1000000" count="other">0 M ¤</pattern>
        …
    </currencyFormat>
</currencyFormatLength>
```

Formats can be supplied for numbers (as above) or for currencies or other units. They can also be used with ranges of numbers, resulting in formatting strings like “$10K” or “$3–7M”.

To format a number N, the greatest type less than or equal to N is used, with the appropriate plural category. N is divided by the type, after removing the number of zeros in the pattern, less 1. APIs supporting this format should provide control over the number of significant or fraction digits.

The default pattern for any type that is not supplied is the special value “0”, as in the following. The value “0” must be used when a child locale overrides a parent locale to drop the compact pattern for that type and use the default pattern.

 `<pattern type="1" count="one">0</pattern>`

If the value is precisely “0”, either explicit or defaulted, then the normal number format pattern for that sort of object is supplied — either `<decimalFormat>` or `<currencyFormat type="standard">` — with the normal formatting for the locale (such as the grouping separators). However, for the “0” case by default the significant digits are adjusted for consistency, typically to 2 or 3 digits, and the maximum fractional digits are set to 0 (for both currencies and plain decimal). Thus the output would be $12, not $12.01. APIs may, however, allow these default behaviors to be overridden.

With the data above, N=12345 matches `<pattern type="10000" count="other">00 K</pattern>`. N is divided by 1000 (obtained from 10000 after removing "00" and restoring one "0"). The result is formatted according to the normal decimal pattern. With no fractional digits, that yields "12 K".

Formatting 1200 in USD would result in “1.2 K $”, while 990 implicitly maps to the special value “0”, which maps to `<currencyFormat type="standard"><pattern>#,##0.00 ¤</pattern>`, and would result in simply “990 $”.

The short format is designed for UI environments where space is at a premium, and should ideally result in a formatted string no more than about 6 em wide (with no fractional digits).

#### <a name="Currency_Formats" href="#Currency_Formats">Currency Formats</a>

Patterns for use with currency formatting:

```dtd
<!ELEMENT currencyFormats (alias | (default*, currencySpacing*, currencyFormatLength*, currencyPatternAppendISO*, unitPattern*, special*)) >
<!ELEMENT currencyFormatLength (alias | (default*, currencyFormat*, special*)) >
<!ATTLIST currencyFormatLength type ( full | long | medium | short ) #IMPLIED >
<!ELEMENT currencyFormat (alias | (pattern*, special*)) >
<!ATTLIST currencyFormat type NMTOKEN "standard" >
    <!--@MATCH:literal/accounting, standard-->
<!ELEMENT currencyPatternAppendISO ( #PCDATA ) >
```

The following additional elements were intended to allow proper placement of the currency symbol relative to the numeric quantity. These are specified in the root locale and typically not overridden in any other locale. However, as of CLDR 42, the preferred approach to controlling placement of the currency symbol is use of the `alt="alphaNextToNumber"` variant for `currencyFormat` `pattern`s. See below and _[- Currencies](#Currencies)_ for additional information on the use of these options.

```dtd
<!ELEMENT currencySpacing (alias | (beforeCurrency*, afterCurrency*, special*)) >
<!ELEMENT beforeCurrency (alias | (currencyMatch*, surroundingMatch*, insertBetween*)) >
<!ELEMENT afterCurrency (alias | (currencyMatch*, surroundingMatch*, insertBetween*)) >
<!ELEMENT currencyMatch ( #PCDATA ) >
<!ELEMENT surroundingMatch ( #PCDATA )) >
<!ELEMENT insertBetween ( #PCDATA ) >
```

In addition to a standard currency format, in which negative currency amounts might typically be displayed as something like “-$3.27”, locales may provide an "accounting" form, in which for "en_US" the same example would appear as “($3.27)”. The locale keyword "cf" can be used to select the standard or accounting form, see [Unicode Currency Format Identifier](tr35.md#UnicodeCurrencyFormatIdentifier).

```xml
<currencyFormats>
    <currencyFormatLength>
        <currencyFormat type="standard">
            <pattern>¤#,##0.00</pattern>
            <pattern alt="alphaNextToNumber">¤ #,##0.00</pattern>
            <pattern alt="noCurrency">#,##0.00</pattern>
        </currencyFormat>
        <currencyFormat type="accounting">
            <pattern>¤#,##0.00;(¤#,##0.00)</pattern>
            <pattern alt="alphaNextToNumber">¤ #,##0.00;(¤ #,##0.00)</pattern>
            <pattern alt="noCurrency">#,##0.00;(#,##0.00)</pattern>
        </currencyFormat>
    </currencyFormatLength>
    <currencyFormatLength type="short">
        <currencyFormat type="standard">
            <pattern type="1000" count="one">¤0K</pattern>
            <pattern type="1000" count="one" alt="alphaNextToNumber">¤ 0K</pattern>
            <pattern type="1000" count="other">¤0K</pattern>
            <pattern type="1000" count="other" alt="alphaNextToNumber">¤ 0K</pattern>
            ...
            <pattern type="100000000000000" count="other">¤000T</pattern>
            <pattern type="100000000000000" count="other" alt="alphaNextToNumber">¤ 000T</pattern>
        </currencyFormat>
    </currencyFormatLength>
</currencyFormats>
```

The `alt="alphaNextToNumber"` pattern, if available, should be used instead of the standard pattern when the currency symbol character closest to the numeric value has Unicode General Category L (letter). The `alt="alphaNextToNumber"` pattern is typically provided when the standard currency pattern does not have a space between currency symbol and numeric value; the alphaNextToNumber variant adds a non-breaking space if appropriate for the locale.

The `alt="noCurrency"` pattern can be used when a currency-style format is desired but without the currency symbol. This sort of display may be used when formatting a large column of values all in the same currency, for example. For compact currency formats (`<currencyFormatLength type="short">`), the compact decimal format (`<decimalFormatLength type="short">`) should be used if no `alt="noCurrency"` pattern is present (so the `alt="noCurrency"` pattern is typically not needed for compact currency formats).

```xml
<currencyPatternAppendISO>{0} ¤¤</currencyPatternAppendISO>
```

The `currencyPatternAppendISO` element provides a pattern that can be used to combine currency format that uses a currency symbol (¤ or ¤¤¤¤¤) with the ISO 4217 3-letter code for the same currency (¤¤), to produce a result such as “$1,432.00 USD”. Using such a format is only recommended to resolve ambiguity when:
* The currency symbol being used is the narrow symbol (¤¤¤¤¤) or has the same value as the narrow symbol, and
* The currency symbol does not have the same value as the ISO 4217 3-letter code.
Most locales will not need to override the pattern provided in root, shown in the xml sample above.

### <a name="Miscellaneous_Patterns" href="#Miscellaneous_Patterns">Miscellaneous Patterns</a>

```dtd
<!ELEMENT miscPatterns (alias | (default*, pattern*, special*)) >
<!ATTLIST miscPatterns numberSystem CDATA #IMPLIED >
```

The miscPatterns supply additional patterns for special purposes. The currently defined values are:

**approximately**

> indicates an approximate number, such as: “\~99”. This pattern is not currently in use; see ICU-20163.

**atMost**

> indicates a number or lower, such as: “`≤`99” to indicate that there are 99 items or fewer.

**atLeast**

> indicates a number or higher, such as: “99+” to indicate that there are 99 items or more.

**range**

> indicates a range of numbers, such as: “99–103” to indicate that there are from 99 to 103 items.

_For example:_

```xml
<miscPatterns numberSystem="…">
  <pattern type="approximately">~{0}</pattern>
  <pattern type="atLeast">≥{0}</pattern>
  <pattern type="atMost">≤{0}</pattern>
  <pattern type="range">{0}–{1}</pattern>
</miscPatterns>
```

### <a name="Minimal_Pairs" href="#Minimal_Pairs">Minimal Pairs</a>

```dtd
<!ELEMENT minimalPairs ( alias | ( pluralMinimalPairs*, ordinalMinimalPairs*, caseMinimalPairs*, genderMinimalPairs*, special* ) ) >
```
```dtd
<!ELEMENT pluralMinimalPairs ( #PCDATA ) >
<!ATTLIST pluralMinimalPairs count NMTOKEN #IMPLIED >
```
```dtd
<!ELEMENT ordinalMinimalPairs ( #PCDATA ) >
<!ATTLIST ordinalMinimalPairs ordinal NMTOKEN #IMPLIED >
```

```dtd
<!ELEMENT caseMinimalPairs ( #PCDATA ) >
<!ATTLIST caseMinimalPairs case NMTOKEN #REQUIRED >
```

```dtd
<!ELEMENT genderMinimalPairs ( #PCDATA ) >
<!ATTLIST genderMinimalPairs gender NMTOKEN #REQUIRED >
```

Minimal pairs provide examples that justify why multiple plural or ordinal categories exist, and for providing contextual examples for verifying consistency of translations. The allowable values for the `count`, `ordinal`, `case`, and `gender` attributes are found in the dtd file.

Examples

```xml
<minimalPairs>
    <pluralMinimalPairs count="one">{0} Tag</pluralMinimalPairs>
    <pluralMinimalPairs count="other">{0} Tage</pluralMinimalPairs>

    <ordinalMinimalPairs ordinal="other">{0}. Abzweigung nach rechts nehmen</ordinalMinimalPairs>

    <caseMinimalPairs case="accusative">… für {0} …</caseMinimalPairs>
    <caseMinimalPairs case="dative">… mit {0} …</caseMinimalPairs>
    <caseMinimalPairs case="genitive">Anstatt {0} …</caseMinimalPairs>
    <caseMinimalPairs case="nominative">{0} kostet (kosten) € 3,50.</caseMinimalPairs>

    <genderMinimalPairs gender="feminine">Die {0} ist …</genderMinimalPairs>
    <genderMinimalPairs gender="masculine">Der {0} ist …</genderMinimalPairs>
    <genderMinimalPairs gender="neuter">Das {0} ist …</genderMinimalPairs>
</minimalPairs>
```


For more information, see [Plural Rules](https://cldr.unicode.org/index/cldr-spec/plural-rules) and [Grammatical Inflection](https://cldr.unicode.org/translation/grammatical-inflection).

## <a name="Number_Format_Patterns" href="#Number_Format_Patterns">Number Format Patterns</a>

### <a name="Number_Patterns" href="#Number_Patterns">Number Patterns</a>

Number patterns affect how numbers are interpreted in a localized context. Here are some examples, based on the French locale. The "." shows where the decimal point should go. The "," shows where the thousands separator should go. A "0" indicates zero-padding: if the number is too short, a zero (in the locale's numeric set) will go there. A "#" indicates no padding: if the number is too short, nothing goes there. A "¤" shows where the currency sign will go. The following illustrates the effects of different patterns for the French locale, with the number "1234.567". Notice how the pattern characters ',' and '.' are replaced by the characters appropriate for the locale.

###### Table: <a name="Number_Pattern_Examples" href="#Number_Pattern_Examples">Number Pattern Examples</a>

| Pattern    | Currency | Text       |
|------------|----------|------------|
| #,##0.##   | _n/a_    | 1 234,57   |
| #,##0.###  | _n/a_    | 1 234,567  |
| ###0.##### | _n/a_    | 1234,567   |
| ###0.0000# | _n/a_    | 1234,5670  |
| 00000.0000 | _n/a_    | 01234,5670 |
| #,##0.00 ¤ | EUR      | 1 234,57 € |
|            | JPY      | 1 235 ¥JP  |

The number of # placeholder characters before the decimal does not matter, since no limit is placed on the maximum number of digits. There should, however, be at least one zero someplace in the pattern. In currency formats, the number of digits after the decimal also does not matter, since the information in the supplemental data (see _[Supplemental Currency Data](#Supplemental_Currency_Data))_ is used to override the number of decimal places — and the rounding — according to the currency that is being formatted. That can be seen in the above chart, with the difference between Yen and Euro formatting.

To ensure correct layout, especially in currency patterns in which a variety of symbols may be used, number patterns may contain (invisible) bidirectional text format characters such as LRM, RLM, and ALM.

_When parsing using a pattern, a lenient parse should be used; see [Lenient Parsing](tr35.md#Lenient_Parsing)._ As noted there, lenient parsing should ignore bidi format characters.

### <a name="Special_Pattern_Characters" href="#Special_Pattern_Characters">Special Pattern Characters</a>

Many characters in a pattern are taken literally; they are matched during parsing and output unchanged during formatting. Special characters, on the other hand, stand for other characters, strings, or classes of characters. For example, the '#' character is replaced by a localized digit for the chosen numberSystem. Often the replacement character is the same as the pattern character; in the U.S. locale, the ',' grouping character is replaced by ','. However, the replacement is still happening, and if the symbols are modified, the grouping character changes. Some special characters affect the behavior of the formatter by their presence; for example, if the percent character is seen, then the value is multiplied by 100 before being displayed.

To insert a special character in a pattern as a literal, that is, without any special meaning, the character must be quoted. There are some exceptions to this which are noted below. The Localized Replacement column shows the replacement from _[Number Symbols](#Number_Symbols)_ or the numberSystem's digits: _italic_ indicates a special function.

Invalid sequences of special characters (such as “¤¤¤¤¤¤” in current CLDR) should be handled for formatting and parsing as described in [Handling Invalid Patterns](tr35.md#Invalid_Patterns).

###### Table: <a name="Number_Pattern_Character_Definitions" href="#Number_Pattern_Character_Definitions">Number Pattern Character Definitions</a>

| Symbol | Location | Localized Replacement | Meaning |
| :-- | :-- | :-- | :-- |
| 0 | Number | digit | Digit |
| 1-9 | Number | digit | '1' through '9' indicate rounding. |
| @ | Number | digit | Significant digit |
| # | Number | digit, _nothing_ | Digit, omitting leading/trailing zeros |
| . | Number | decimal, currencyDecimal | Decimal separator or monetary decimal separator |
| - | Number | minusSign, plusSign, approximatelySign | Minus sign. **Warning:** the pattern '-'0.0 is not the same as the pattern -0.0. In the former case, the minus sign is a literal. In the latter case, it is a special symbol, which is replaced by the minusSymbol, and can also be replaced by the plusSymbol for a format like +12% as in [Explicit Plus Signs](#Explicit_Plus). |
| , | Number | group, currencyGroup | Grouping separator. May occur in both the integer part and the fractional part. The position determines the grouping. |
| E | Number | exponential, superscriptingExponent | Separates mantissa and exponent in scientific notation. _Need not be quoted in prefix or suffix._ |
| + | Exponent or Number (for explicit plus) | plusSign | Prefix positive exponents with localized plus sign. Used for explicit plus for numbers as well, as described in [Explicit Plus Signs](#Explicit_Plus). _Need not be quoted in prefix or suffix._ |
| % | Prefix or suffix | percentSign | Multiply by 100 and show as percentage |
| ‰ (U+2030) | Prefix or suffix | perMille | Multiply by 1000 and show as per mille (aka “basis points”) |
| ; | Subpattern boundary | _syntax_ | Separates positive and negative subpatterns. When there is no explicit negative subpattern, an implicit negative subpattern is formed from the positive pattern with a prefixed - (ASCII U+002D HYPHEN-MINUS). |
| ¤ (U+00A4) | Prefix or suffix | _currency symbol/name from currency specified in API_ | Any sequence is replaced by the localized currency symbol for the currency being formatted, as in the table below. If present in a pattern, the monetary decimal separator and grouping separators (if available) are used instead of the numeric ones. If data is unavailable for a given sequence in a given locale, the display may fall back to ¤ or ¤¤. See also the formatting for currency display names, steps 2 and 4 in [Currencies](#Currencies). <table><tr><th>No.</th><th>Replacement / Example</th></tr><tr><td rowspan="2">¤</td><td>Standard currency symbol</td></tr><tr><td>_C$12.00_</td></tr><tr><td rowspan="2">¤¤</td><td>ISO currency symbol (constant)</td></tr><tr><td>_CAD 12.00_</td></tr><tr><td rowspan="2">¤¤¤</td><td>Appropriate currency display name for the currency, based on the plural rules in effect for the locale</td></tr><tr><td>_5.00 Canadian dollars_</td></tr><tr><td rowspan="2" >¤¤¤¤¤</td><td>Narrow currency symbol. The same symbols may be used for multiple currencies. Thus the symbol may be ambiguous, and should only be where the context is clear.</td></tr><tr><td>_$12.00_</td></tr><tr><td>_others_</td><td>_Invalid in current CLDR. Reserved for future specification_</td></tr></table> |
| * | Prefix or suffix boundary | _padding character specified in API_ | Pad escape, precedes pad character |
| ' | Prefix or suffix | _syntax-only_ | Used to quote special characters in a prefix or suffix, for example, `"'#'#"` formats 123 to `"#123"`. To create a single quote itself, use two in a row: `"# o''clock"`. |

A pattern contains a positive subpattern and may contain a negative subpattern, for example, "#,##0.00;(#,##0.00)". Each subpattern has a prefix, a numeric part, and a suffix. If there is no explicit negative subpattern, the implicit negative subpattern is the ASCII minus sign (-) prefixed to the positive subpattern. That is, "0.00" alone is equivalent to "0.00;-0.00". (The data in CLDR is normalized to remove an explicit negative subpattern where it would be identical to the implicit form.)

Note that if a negative subpattern is used as-is: a minus sign is _not_ added, eg "0.00;0.00" ≠ "0.00;-0.00". Trailing semicolons are ignored, eg "0.00;" = "0.00". Whitespace is not ignored, including those around semicolons, so "0.00 ; -0.00" ≠ "0.00;-0.00".

If there is an explicit negative subpattern, it serves only to specify the negative prefix and suffix; the number of digits, minimal digits, and other characteristics are ignored in the negative subpattern. That means that "#,##0.0#;(#)" has precisely the same result as "#,##0.0#;(#,##0.0#)". However in the CLDR data, the format is normalized so that the other characteristics are preserved, just for readability.

> **Note:** The thousands separator and decimal separator in patterns are always ASCII ',' and '.'. They are substituted by the code with the correct local values according to other fields in CLDR. The same is true of the - (ASCII minus sign) and other special characters listed above.

A currency decimal pattern normally contains a currency symbol placeholder (¤, ¤¤, ¤¤¤, or ¤¤¤¤¤). The currency symbol placeholder may occur before the first digit, after the last digit symbol, or where the decimal symbol would otherwise be placed (for formats such as "12€50", as in "12€50 pour une omelette").

| Placement | Examples                                                                         |
|-----------|----------------------------------------------------------------------------------|
| Before    | "¤#,##0.00" "¤ #,##0.00" "¤-#,##0.00" "¤ -#,##0.00" "-¤#,##0.00" "-¤ #,##0.00" … |
| After     | "#,##0.00¤" "#,##0.00 ¤" "#,##0.00-¤" "#,##0.00- ¤" "#,##0.00¤-" "#,##0.00 ¤-" … |
| Decimal   | "#,##0¤00"                                                                       |

Below is a sample of patterns, special characters, and results:

###### Table: <a name="Sample_Patterns_and_Results" href="#Sample_Patterns_and_Results">Sample Patterns and Results</a>

<table><tbody>
<tr><th>explicit pattern:</th><td colspan="2">0.00;-0.00</td><td colspan="2">0.00;0.00-</td><td colspan="2">0.00+;0.00-</td></tr>
<tr><th>decimalSign:</th><td colspan="2">,</td><td colspan="2">,</td><td colspan="2">,</td></tr>
<tr><th>minusSign:</th><td colspan="2">∸</td><td colspan="2">∸</td><td colspan="2">∸</td></tr>
<tr><th>plusSign:</th><td colspan="2">∔</td><td colspan="2">∔</td><td colspan="2">∔</td></tr>
<tr><th>number:</th><td>3.1415</td><td>-3.1415</td><td>3.1415</td><td>-3.1415</td><td>3.1415</td><td>-3.1415</td></tr>
<tr><th>formatted:</th><td>3,14</td><td>∸3,14</td><td>3,14</td><td>3,14∸</td><td>3,14∔</td><td>3,14∸</td></tr>
</tbody></table>

_In the above table, ∸ = U+2238 DOT MINUS and ∔ = U+2214 DOT PLUS are used for illustration._

The prefixes, suffixes, and various symbols used for infinity, digits, thousands separators, decimal separators, and so on may be set to arbitrary values, and they will appear properly during formatting. _However, care must be taken that the symbols and strings do not conflict, or parsing will be unreliable._ For example, either the positive and negative prefixes or the suffixes must be distinct for any parser using this data to be able to distinguish positive from negative values. Another example is that the decimal separator and thousands separator should be distinct characters, or parsing will be impossible.

The _grouping separator_ is a character that separates clusters of integer digits to make large numbers more legible. It is commonly used for thousands, but in some locales it separates ten-thousands. The _grouping size_ is the number of digits between the grouping separators, such as 3 for "100,000,000" or 4 for "1 0000 0000". There are actually two different grouping sizes: One used for the least significant integer digits, the _primary grouping size_, and one used for all others, the _secondary grouping size_. In most locales these are the same, but sometimes they are different. For example, if the primary grouping interval is 3, and the secondary is 2, then this corresponds to the pattern "#,##,##0", and the number 123456789 is formatted as "12,34,56,789". If a pattern contains multiple grouping separators, the interval between the last one and the end of the integer defines the primary grouping size, and the interval between the last two defines the secondary grouping size. All others are ignored, so "#,##,###,####" == "###,###,####" == "##,#,###,####".

The grouping separator may also occur in the fractional part, such as in “#,##0.###,#”. This is most commonly done where the grouping separator character is a thin, non-breaking space (U+202F), such as “1.618 033 988 75”. See [physics.nist.gov/cuu/Units/checklist.html](https://physics.nist.gov/cuu/Units/checklist.html).

For consistency in the CLDR data, the following conventions are observed:

* All number patterns should be minimal: there should be no leading # marks except to specify the position of the grouping separators (for example, avoid  ##,##0.###).
* All formats should have one 0 before the decimal point (for example, avoid #,###.##)
* Decimal formats should have three hash marks in the fractional position (for example, #,##0.###).
* Currency formats should have two zeros in the fractional position (for example, ¤ #,##0.00).
    * The exact number of decimals is overridden with the decimal count in supplementary data or by API settings.
* The only time two thousands separators need to be used is when the number of digits varies, such as for Hindi: #,##,##0.
* The **minimumGroupingDigits** can be used to suppress groupings below a certain value. This is used for languages such as Polish, where one would only write the grouping separator for values above 9999. The minimumGroupingDigits contains the default for the locale.
    * The attribute value is used by adding it to the grouping separator value. If the input number has fewer integer digits, the grouping separator is suppressed.
    * ##### <a name="Examples_of_minimumGroupingDigits" href="#Examples_of_minimumGroupingDigits">Examples of minimumGroupingDigits</a>

        | minimum­GroupingDigits | Pattern Grouping | Input Number | Formatted |
        |-----------------------:|-----------------:|-------------:|----------:|
        |                      1 |                3 |         1000 |     1,000 |
        |                      1 |                3 |        10000 |    10,000 |
        |                      2 |                3 |         1000 |      1000 |
        |                      2 |                3 |        10000 |    10,000 |
        |                      1 |                4 |        10000 |    1,0000 |
        |                      2 |                4 |        10000 |     10000 |

#### <a name="Explicit_Plus" href="#Explicit_Plus">Explicit Plus Signs</a>

An explicit "plus" format can be formed, so as to show a visible + sign when formatting a non-negative number. The displayed plus sign can be an ASCII plus or another character, such as ＋ U+FF0B FULLWIDTH PLUS SIGN or ➕ U+2795 HEAVY PLUS SIGN; it is taken from whatever is set for plusSign in _[Number Symbols](#Number_Symbols)_.

1. Get the negative subpattern (explicit or implicit).
2. Replace any unquoted ASCII minus sign by an ASCII plus sign.
3. If there are any replacements, use that for the positive subpattern.

For an example, see [Sample Patterns and Results](#Sample_Patterns_and_Results).

### <a name="Formatting" href="#Formatting">Formatting</a>

Formatting is guided by several parameters, all of which can be specified either using a pattern or using an external API designed for number formatting. The following description applies to formats that do not use [scientific notation](#sci) or [significant digits](#sigdig).

* If the number of actual integer digits exceeds the _maximum integer digits_, then only the least significant digits are shown. For example, 1997 is formatted as "97" if the maximum integer digits is set to 2.
* If the number of actual integer digits is less than the _minimum integer digits_, then leading zeros are added. For example, 1997 is formatted as "01997" if the minimum integer digits is set to 5.
* If the number of actual fraction digits exceeds the _maximum fraction digits_, then half-even rounding it performed to the maximum fraction digits. For example, 0.125 is formatted as "0.12" if the maximum fraction digits is 2. This behavior can be changed by specifying a rounding increment and a rounding mode.
* If the number of actual fraction digits is less than the _minimum fraction digits_, then trailing zeros are added. For example, 0.125 is formatted as "0.1250" if the minimum fraction digits is set to 4.
* Trailing fractional zeros are not displayed if they occur _j_ positions after the decimal, where _j_ is less than the maximum fraction digits. For example, 0.10004 is formatted as "0.1" if the maximum fraction digits is four or less.

**Special Values**

`NaN` is represented as a single character, typically `(U+FFFD)` . This character is determined by the localized number symbols. This is the only value for which the prefixes and suffixes are not used.

Infinity is represented as a single character, typically ∞ `(U+221E)` , with the positive or negative prefixes and suffixes applied. The infinity character is determined by the localized number symbols.

### <a name="sci" href="#sci">Scientific Notation</a>

Numbers in scientific notation are expressed as the product of a mantissa and a power of ten, for example, 1234 can be expressed as 1.234 x 10<sup>3</sup>. The mantissa is typically in the half-open interval [1.0, 10.0) or sometimes [0.0, 1.0), but it need not be. In a pattern, the exponent character immediately followed by one or more digit characters indicates scientific notation. Example: "0.###E0" formats the number 1234 as "1.234E3".

* The number of digit characters after the exponent character gives the minimum exponent digit count. There is no maximum. Negative exponents are formatted using the localized minus sign, _not_ the prefix and suffix from the pattern. This allows patterns such as "0.###E0 m/s". To prefix positive exponents with a localized plus sign, specify '+' between the exponent and the digits: "0.###E+0" will produce formats "1E+1", "1E+0", "1E-1", and so on. (In localized patterns, use the localized plus sign rather than '+'.)
* The minimum number of integer digits is achieved by adjusting the exponent. Example: 0.00123 formatted with "00.###E0" yields "12.3E-4". This only happens if there is no maximum number of integer digits. If there is a maximum, then the minimum number of integer digits is fixed at one.
* The maximum number of integer digits, if present, specifies the exponent grouping. The most common use of this is to generate _engineering notation_, in which the exponent is a multiple of three, for example, "##0.###E0". The number 12345 is formatted using "##0.####E0" as "12.345E3".
* When using scientific notation, the formatter controls the digit counts using logic for significant digits. The maximum number of significant digits comes from the mantissa portion of the pattern: the string of #, 0, and period (".") characters immediately preceding the E. To get the maximum number of significant digits, use the following algorithm:

    1.  If the mantissa pattern contains a period:
        1.  If the mantissa pattern contains at least one 0:
            *   Return the number of 0s before the period added to the number of #s or 0s after the period
        2.  Else:
            *   Return 1 plus the number of #s after the period
    2.  Else:
        1.  If the mantissa pattern contains at least one 0:
            *   Return the number of 0s.
        2.  Else:
            *   Return positive infinity.

    Examples:

    *   0.##E0 means a max of 3 significant digits.
    *   #.##E0 also means a max of 3 significant digits.
    *   #.0#E0 means a max of 2 significant digits.
    *   0E0 means a max of 1 significant digit.
    *   #E0 means infinite precision.
    *   ###E0 means engineering notation with infinite precision.
*   Exponential patterns may not contain grouping separators.

### <a name="sigdig" href="#sigdig">Significant Digits</a>

There are two ways of controlling how many digits are shown: (a) significant digits counts, or (b) integer and fraction digit counts. Integer and fraction digit counts are described above. When a formatter is using significant digits counts, it uses however many integer and fraction digits are required to display the specified number of significant digits. It may ignore min/max integer/fraction digits, or it may use them to the extent possible.

###### Table: <a name="Significant_Digits_Examples" href="#Significant_Digits_Examples">Significant Digits Examples</a>

| Pattern | Minimum significant digits | Maximum significant digits | Number | Output |
| :-- | :-- | :-- | :-- | :-- |
| `@@@` | 3 | 3 | 12345 | `12300` |
| `@@@` | 3 | 3 | 0.12345 | `0.123` |
| `@@##` | 2 | 4 | 3.14159 | `3.142` |
| `@@##` | 2 | 4 | 1.23004 | `1.23` |

* In order to enable significant digits formatting, use a pattern containing the `'@'` pattern character. In order to disable significant digits formatting, use a pattern that does not contain the `'@'` pattern character.
* Significant digit counts may be expressed using patterns that specify a minimum and maximum number of significant digits. These are indicated by the `'@'` and `'#'` characters. The minimum number of significant digits is the number of `'@'` characters. The maximum number of significant digits is the number of `'@'` characters plus the number of `'#'` characters following on the right. For example, the pattern `"@@@"` indicates exactly 3 significant digits. The pattern `"@##"` indicates from 1 to 3 significant digits. Trailing zero digits to the right of the decimal separator are suppressed after the minimum number of significant digits have been shown. For example, the pattern `"@##"` formats the number 0.1203 as `"0.12"`.
* Implementations may forbid the use of significant digits in combination with min/max integer/fraction digits. In such a case, if a pattern uses significant digits, it may not contain a decimal separator, nor the `'0'` pattern character. Patterns such as `"@00"` or `"@.###"` would be disallowed.
* Any number of `'#'` characters may be prepended to the left of the leftmost `'@'` character. These have no effect on the minimum and maximum significant digits counts, but may be used to position grouping separators. For example, `"#,#@#"` indicates a minimum of one significant digit, a maximum of two significant digits, and a grouping size of three.
* The number of significant digits has no effect on parsing.
* Significant digits may be used together with exponential notation. Such patterns are equivalent to a normal exponential pattern with a minimum and maximum integer digit count of one, a minimum fraction digit count of `Minimum Significant Digits - 1`, and a maximum fraction digit count of `Maximum Significant Digits - 1`. For example, the pattern `"@@###E0"` is equivalent to `"0.0###E0"`.

### <a name="Padding" href="#Padding">Padding</a>

Patterns support padding the result to a specific width. In a pattern the pad escape character, followed by a single pad character, causes padding to be parsed and formatted. The pad escape character is '*'. For example, `"$*x#,##0.00"` formats 123 to `"$xx123.00"` , and 1234 to `"$1,234.00"` .

* When padding is in effect, the width of the positive subpattern, including prefix and suffix, determines the format width. For example, in the pattern `"* #0 o''clock"`, the format width is 10.
* Some parameters which usually do not matter have meaning when padding is used, because the pattern width is significant with padding. In the pattern "* ##,##,#,##0.##", the format width is 14. The initial characters "##,##," do not affect the grouping size or maximum integer digits, but they do affect the format width.
* Padding may be inserted at one of four locations: before the prefix, after the prefix, before the suffix, or after the suffix. No padding can be specified in any other location. If there is no prefix, before the prefix and after the prefix are equivalent, likewise for the suffix.
* When specified in a pattern, the code point immediately following the pad escape is the pad character. This may be any character, including a special pattern character. That is, the pad escape _escapes_ the following character. If there is no character after the pad escape, then the pattern is illegal.

### <a name="Rounding" href="#Rounding">Rounding</a>

Patterns support rounding to a specific increment. For example, 1230 rounded to the nearest 50 is 1250. Mathematically, rounding to specific increments is performed by dividing by the increment, rounding to an integer, then multiplying by the increment. To take a more bizarre example, 1.234 rounded to the nearest 0.65 is 1.3, as follows:

<table><tbody>
<tr><th>Original:</th><td>1.234</td></tr>
<tr><th>Divide by increment (0.65):</th><td>1.89846…</td></tr>
<tr><th>Round:</th><td>2</td></tr>
<tr><th>Multiply by increment (0.65):</th><td>1.3</td></tr>
</tbody></table>

To specify a rounding increment in a pattern, include the increment in the pattern itself. "#,#50" specifies a rounding increment of 50. "#,##0.05" specifies a rounding increment of 0.05.

* Rounding only affects the string produced by formatting. It does not affect parsing or change any numerical values.
* An implementation may allow the specification of a _rounding mode_ to determine how values are rounded. In the absence of such choices, the default is to round "half-even", as described in IEEE arithmetic. That is, it rounds towards the "nearest neighbor" unless both neighbors are equidistant, in which case, it rounds towards the even neighbor. Behaves as for round "half-up" if the digit to the left of the discarded fraction is odd; behaves as for round "half-down" if it's even. Note that this is the rounding mode that minimizes cumulative error when applied repeatedly over a sequence of calculations.
* Some locales use rounding in their currency formats to reflect the smallest currency denomination.
* In a pattern, digits '1' through '9' specify rounding, but otherwise behave identically to digit '0'.

### <a name="Quoting_Rules" href="#Quoting_Rules">Quoting Rules</a>

Single quotes (**'**) enclose bits of the pattern that should be treated literally. Inside a quoted string, two single quotes ('') are replaced with a single one ('). For example: `'X '`#`' Q '` -> **X 1939 Q** (Literal strings `shaded`.)

## <a name="Currencies" href="#Currencies">Currencies</a>

```dtd
<!ELEMENT currencies (alias | (default?, currency*, special*)) >
<!ELEMENT currency (alias | (((pattern+, displayName*, symbol*) | (displayName+, symbol*, pattern*) | (symbol+, pattern*))?, decimal*, group*, special*)) >
<!ELEMENT symbol ( #PCDATA ) >
<!ATTLIST symbol choice ( true | false ) #IMPLIED > <!-- deprecated -->
```

> **Note:** The term "pattern" appears twice in the above. The first is for consistency with all other cases of pattern + displayName; the second is for backwards compatibility.

```xml
<currencies>
    <currency type="USD">
        <displayName>Dollar</displayName>
        <symbol>$</symbol>
    </currency>
    <currency type ="JPY">
        <displayName>Yen</displayName>
        <symbol>¥</symbol>
    </currency>
    <currency type="PTE">
        <displayName>Escudo</displayName>
        <symbol>$</symbol>
    </currency>
</currencies>
```

In formatting currencies, the currency number format is used with the appropriate symbol from `<currencies>`, according to the currency code. The `<currencies>` list can contain codes that are no longer in current use, such as PTE. The `choice` attribute has been deprecated.

The `count` attribute distinguishes the different plural forms, such as in the following:

```xml
<currencyFormats>
    <unitPattern count="other">{0} {1}</unitPattern>
    …
</currencyFormats>
```

```xml
<currency type="ZWD">
    <displayName>Zimbabwe Dollar</displayName>
    <displayName count="one">Zimbabwe dollar</displayName>
    <displayName count="other">Zimbabwe dollars</displayName>
    <symbol>Z$</symbol>
</currency>
```

Note on displayNames:
* In general the region portion of the displayName should match the territory name, see **Part 2** _[Locale Display Name Fields](tr35-general.md#locale_display_name_fields)_.
* As a result, the English currency displayName in CLDR may not match the name in ISO 4217.

To format a particular currency value "ZWD" for a particular numeric value _n_ using the (long) display name:

1. If the numeric value is exactly 0 or 1, first see if there is a count with a matching explicit number (0 or 1). If so, use that string (see [Explicit 0 and 1 rules](#Explicit_0_1_rules)).
2. Otherwise, determine the `count` value that corresponds to _n_ using the rules in _[- Language Plural Rules](#Language_Plural_Rules)_
3. Next, get the currency unitPattern.
   1. Look for a `unitPattern` element that matches the `count` value, starting in the current locale and then following the locale fallback chain up to, but not including root.
   2. If no matching `unitPattern` element was found in the previous step, then look for a `unitPattern` element that matches `count="other"`, starting in the current locale and then following the locale fallback chain up to root (which has a `unitPattern` element with `count="other"` for every unit type).
   3. The resulting unitPattern element indicates the appropriate positioning of the numeric value and the currency display name.
4. Next, get the `displayName` element for the currency.
   1. Look for a `displayName` element that matches the `count` value, starting in the current locale and then following the locale fallback chain up to, but not including root.
   2. If no matching `displayName` element was found in the previous step, then look for a `displayName` element that matches `count="other"`, starting in the current locale and then following the locale fallback chain up to, but not including root.
   3. If no matching `displayName` element was found in the previous step, then look for a `displayName` element with no count, starting in the current locale and then following the locale fallback chain up to root.
   4. If there is no `displayName` element, use the currency code itself (for example, "ZWD").
5. Format the numeric value according to the locale. Use the locale’s `<decimalFormats …>` pattern, not the `<currencyFormats>` pattern that is used with the symbol (eg, Z$). As when formatting symbol currency values, reset the number of decimals according to the supplemental `<currencyData>` and use the currencyDecimal symbol if different from the decimal symbol.
   1. The number of decimals should be overridable in an API, so that clients can choose between “2 US dollars” and “2.00 US dollars”.
6. Substitute the formatted numeric value for the {0} in the `unitPattern`, and the currency display name for the {1}.

While for English this may seem overly complex, for some other languages different plural forms are used for different unit types; the plural forms for certain unit types may not use all of the plural-form tags defined for the language.

For example, if the currency is ZWD and the number is 1234, then the latter maps to `count="other"` for English. The unit pattern for that is "{0} {1}", and the display name is "Zimbabwe dollars". The final formatted number is then "1,234 Zimbabwe dollars".

---

When a currency symbol is substitited into a pattern, some spacing adjustments or other adjustments may be necessary depending on the nature of the symbol. In CLDR 42 and later, the preferred way to handle this is via the `alt="alphaNextToNumber"` variant of the `currencyFormat` `pattern`, as described in _[Section 2.4.2: Currency Formats](#Currency_Formats)_. In earlier versions of CLDR this was handled via the `currencySpacing` element as described below. This element is still present in CLDR 42 and its use is described below for implementations that may not yet support the `alt="alphaNextToNumber"` variant of the `currencyFormat` `pattern`.

```xml
<currencySpacing>
  <beforeCurrency>
    <currencyMatch>[:^S:]</currencyMatch>
    <surroundingMatch>[:digit:]</surroundingMatch>
    <insertBetween> </insertBetween>
  </beforeCurrency>
  <afterCurrency>
    <currencyMatch>[:^S:]</currencyMatch>
    <surroundingMatch>[:digit:]</surroundingMatch>
    <insertBetween> </insertBetween>
  </afterCurrency>
</currencySpacing>
```

This element controls whether additional characters are inserted on the boundary between the symbol and the pattern. For example, with the above `currencySpacing`, inserting the symbol "US\$" into the pattern "#,##0.00¤" would result in an extra _no-break space_ inserted before the symbol, for example, "#,##0.00 US\$". The `beforeCurrency` element governs this case, since we are looking _before_ the "¤" symbol. The `currencyMatch` is positive, since the "U" in "US\$" is at the start of the currency symbol being substituted. The `surroundingMatch` is positive, since the character just before the "¤" will be a digit. Because these two conditions are true, the insertion is made.

Conversely, look at the pattern "¤#,##0.00" with the symbol "US$". In this case, there is no insertion; the result is simply "US$#,##0.00". The `afterCurrency` element governs this case, since we are looking _after_ the "¤" symbol. The `surroundingMatch` is positive, since the character just after the "¤" will be a digit. However, the `currencyMatch` is **not** positive, since the "\$" in "US\$" is at the end of the currency symbol being substituted. So the insertion is not made.

For more information on the matching used in the `currencyMatch` and `surroundingMatch` elements, see the main document _[Appendix E: Unicode Sets](tr35.md#Unicode_Sets)_.

---

Currencies can also contain optional grouping, decimal data, and pattern elements. This data is inherited from the `<symbols>` in the same locale data (if not present in the chain up to root), so only the _differing_ data will be present. See the main document _[Multiple Inheritance](tr35.md#Multiple_Inheritance)_.

> **Note:** _Currency values should **never** be interchanged without a known currency code. You never want the number 3.5 interpreted as $3.50 by one user and €3.50 by another._ Locale data contains localization information for currencies, not a currency value for a country. A currency amount logically consists of a numeric value, plus an accompanying currency code (or equivalent). The currency code may be implicit in a protocol, such as where USD is implicit. But if the raw numeric value is transmitted without any context, then it has no definitive interpretation.

Notice that the currency code is completely independent of the end-user's language or locale. For example, BGN is the code for Bulgarian Lev. A currency amount of <BGN, 1.23456×10³> would be localized for a Bulgarian user into "1 234,56 лв." (using Cyrillic letters). For an English user it would be localized into the string "BGN 1,234.56". The end-user's language is needed for doing this last localization step; but that language is completely orthogonal to the currency code needed in the data. After all, the same English user could be working with dozens of currencies. Notice also that the currency code is also independent of whether currency values are inter-converted, which requires more interesting financial processing: the rate of conversion may depend on a variety of factors.

Thus logically speaking, once a currency amount is entered into a system, it should be logically accompanied by a currency code in all processing. This currency code is independent of whatever the user's original locale was. Only in badly-designed software is the currency code (or equivalent) not present, so that the software has to "guess" at the currency code based on the user's locale.

> **Note:** The number of decimal places **and** the rounding for each currency is not locale-specific data, and is not contained in the Locale Data Markup Language format. Those values override whatever is given in the currency numberFormat. For more information, see _[Supplemental Currency Data](#Supplemental_Currency_Data)_.

For background information on currency names, see [[CurrencyInfo](tr35.md#CurrencyInfo)].

### <a name="Supplemental_Currency_Data" href="#Supplemental_Currency_Data">Supplemental Currency Data</a>

```dtd
<!ELEMENT currencyData ( fractions*, region+ ) >
<!ELEMENT fractions ( info+ ) >

<!ELEMENT info EMPTY >
<!ATTLIST info iso4217 NMTOKEN #REQUIRED >
<!ATTLIST info digits NMTOKEN #IMPLIED >
<!ATTLIST info rounding NMTOKEN #IMPLIED >
<!ATTLIST info cashDigits NMTOKEN #IMPLIED >
<!ATTLIST info cashRounding NMTOKEN #IMPLIED >

<!ELEMENT region ( currency* ) >
<!ATTLIST region iso3166 NMTOKEN #REQUIRED >

<!ELEMENT currency ( alternate* ) >
<!ATTLIST currency iso4217 NMTOKEN #REQUIRED >
<!ATTLIST currency from NMTOKEN #IMPLIED >
<!ATTLIST currency to NMTOKEN #IMPLIED >
<!ATTLIST currency tz CDATA #IMPLIED >
<!ATTLIST currency to-tz CDATA #IMPLIED >
<!ATTLIST currency tender ( true | false ) #IMPLIED >
```

Each `currencyData` element contains one `fractions` element followed by one or more `region` elements. Here is an example for illustration.

```xml
<supplementalData>
    <currencyData>
        <fractions>
        …
        <info iso4217="CHF" digits="2" rounding="5"/>
        …
        <info iso4217="ITL" digits="0"/>
        …
        </fractions>
        …
        <region iso3166="IT">
            <currency iso4217="EUR" from="1999-01-01"/>
            <currency iso4217="ITL" from="1862-8-24" to="2002-02-28"/>
        </region>
        …
        <region iso3166="CS">
            <currency iso4217="EUR" from="2003-02-04"/>
            <currency iso4217="CSD" from="2002-05-15"/>
            <currency iso4217="YUM" from="1994-01-24" to="2002-05-15"/>
        </region>
        …
    </currencyData>
    …
</supplementalData>
```

The `fractions` element contains any number of `info` elements, with the following attributes:

* **iso4217:** the ISO 4217 code for the currency in question. If a particular currency does not occur in the fractions list, then it is given the defaults listed for the next two attributes.
* **digits:** the minimum and maximum number of decimal digits normally formatted. 
The default is 2. 
For example, in the en_US locale with the default value of 2 digits, the value 1 USD would format as "$1.00", and the value 1.123 USD would format as → "$1.12". 
This value of this field is based on the "minor unit" value from ISO 4217, but may deviate from ISO 4217 where there is compelling evidence for different customary practice.
* **rounding:** the rounding increment, in units of 10<sup>-digits</sup>. The default is 0, which means no rounding is to be done. Therefore, rounding=0 and rounding=1 have identical behavior. Thus with fraction digits of 2 and rounding increment of 5, numeric values are rounded to the nearest 0.05 units in formatting. With fraction digits of 0 and rounding increment of 50, numeric values are rounded to the nearest 50.
* **cashDigits:** the number of decimal digits to be used when formatting quantities used in cash transactions (as opposed to a quantity that would appear in a more formal setting, such as on a bank statement). If absent, the value of "digits" should be used as a default.
* **cashRounding:** the cash rounding increment, in units of 10-cashDigits. The default is 0, which means no rounding is to be done; and as with rounding, this has the same effect as cashRounding="1". This is the rounding increment to be used when formatting quantities used in cash transactions (as opposed to a quantity that would appear in a more formal setting, such as on a bank statement). If absent, the value of "rounding" should be used as a default.

For example, the following line

```xml
<info iso4217="CZK" digits="2" rounding="0"/>
```

should cause the value 2.006 to be displayed as “2.01”, not “2.00”.

Each `region` element contains one attribute:

* **iso3166:** the ISO 3166 code for the region in question. The special value _XXX_ can be used to indicate that the region has no valid currency or that the circumstances are unknown (usually used in conjunction with _before_, as described below).

And can have any number of `currency` elements, with the `ordered` subelements.

```xml
<region iso3166="HR"> <!-- Croatia -->
    <currency iso4217="EUR" from="2023-01-01" tz="Europe/Zagreb"/>
    <currency iso4217="HRK" from="1994-05-30" to="2023-01-14" to-tz="Europe/Zagreb"/>
    <currency iso4217="HRD" from="1991-12-23" to="1995-01-01"/>
    <currency iso4217="YUN" from="1990-01-01" to="1991-12-23"/>
    <currency iso4217="YUD" from="1966-01-01" to="1990-01-01"/>
</region>
```

* **iso4217:** the ISO 4217 code for the currency in question. Note that some additional codes that were in widespread usage are included, others such as GHP are not included because they were never used.
* **from:** the currency was valid from the datetime indicated by the value. See the main document _[Dates and Date Ranges](tr35.md#Date_Ranges)_.
* **to:** the currency was valid up to the datetime indicated by the value. See the main document _[Dates and Date Ranges](tr35.md#Date_Ranges)_.
* **tz:** the timezone associated with the `from` transition datetime. If no `to-tz` attribute is specified, it also applies to the `to` transition datetime.
    * Timezones for the `tz` and `to-tz` attribute are specified using the CLDR canonical “long” time zone ID as described under **Stability of Time Zone Identifiers** in [Time Zone Identifiers](tr35.md#Time_Zone_Identifiers).
* **to-tz:** the timezone associated with the `to` transition datetime. This timezone applies to the `to` value, and need only be specified if it is different from any timezone specified by a `tz` attribute.
* **tender:** indicates whether the ISO currency code represents a currency that was or is legal tender in some country. The default is "true". Certain ISO codes represent things like financial instruments or precious metals, and do not represent normally interchanged currencies.

> **Note on converting transition datetimes for implementation use:** The fact that CLDR stores the timezone separately from the transition datetime is for ease of CLDR maintenance. Implementations that use CLDR data may want to convert the combination into something like a single UTC timestamp for internal use.

That is, each `currency` element will list an interval in which it was valid. The _ordering_ of the elements in the list tells us which was the primary currency during any period in time. Here is an example of such an overlap:

```xml
<currency iso4217="CSD" to="2002-05-15"/>
<currency iso4217="YUD" from="1994-01-24" to="2002-05-15"/>
<currency iso4217="YUN" from="1994-01-01" to="1994-07-22"/>
```

All `currency` elements with `tender="false"` should be at the end of the list for a given `region`. 

The `from` element is limited by the fact that ISO 4217 does not go very far back in time, so there may be no ISO code for the previous currency.

Currencies change relatively frequently. There are different types of changes:

1. YU=>CS (name change)
2. CS=>RS+ME (split, different names)
3. SD=>SD+SS (split, same name for one // South Sudan splits from Sudan)
4. DE+DD=>DE (Union, reuses one name // East Germany unifies with Germany)

The [UN Information](https://unstats.un.org/unsd/methodology/m49/) is used to determine dates due to country changes.

When a code is no longer in use, it is terminated (see #1, #2, #4, #5)

> Example:
>
> * ```<currency iso4217="EUR" from="2003-02-04" to="2006-06-03"/>```

When codes split, each of the new codes inherits (see #2, #3) the previous data. However, some modifications can be made if it is clear that currencies were only in use in one of the parts.

When codes merge, the data is copied from the most populous part.

> Example. When CS split into RS and ME:
>
> * RS & ME copy the former CS, except that the line for EUR is dropped from RS
> * CS now terminates on Jun 3, 2006 (following the UN info)

## <a name="Language_Plural_Rules" href="#Language_Plural_Rules">Language Plural Rules</a>

```dtd
<!ELEMENT plurals (pluralRules*, pluralRanges*) >
<!ATTLIST plurals type ( ordinal | cardinal ) #IMPLIED > <!-- default is cardinal -->

<!ELEMENT pluralRules (pluralRule*) >
<!ATTLIST pluralRules locales NMTOKENS #REQUIRED >

<!ELEMENT pluralRule ( #PCDATA ) >
<!ATTLIST pluralRule count (zero | one | two | few | many | other) #REQUIRED >
```

The plural categories are used to format messages with numeric placeholders, expressed as decimal numbers. The fundamental rule for determining plural categories is the existence of minimal pairs: whenever two different numbers may require different versions of the same message, then the numbers have different plural categories.

This happens even if nouns are invariant; even if all English nouns were invariant (like “sheep”), English would still require 2 plural categories because of subject-verb agreement, and pronoun agreement. For example:

1. 1 sheep **is** here. Do you want to buy **it**?
2. 2 sheep **are** here. Do you want to buy **them**?

For more information, see [Determining-Plural-Categories](https://cldr.unicode.org/index/cldr-spec/plural-rules#determining-plural-categories).

English does not have a separate plural category for “zero”, because it does not require a different message for “0”. For example, the same message can be used below, with just the numeric placeholder changing.

1. You have 3 friends online.
2. You have 0 friends online.

However, across many languages it is commonly more natural to express "0" messages with a negative (“None of your friends are online.”) and "1" messages also with an alternate form “You have a friend online.”. Thus pluralized message APIs should also offer the ability to specify at least the 0 and 1 cases explicitly; developers can use that ability whenever these values might occur in a placeholder.

The CLDR plural rules are not expected to cover all cases. For example, strictly speaking, there could be more plural and ordinal forms for English. Formally, we have a different plural form where a change in digits forces a change in the rest of the sentence. There is an edge case in English because of the behavior of "a/an".

For example, in changing from 3 to 8:

* "a 3rd of a loaf" should result in "an 8th of a loaf", not "a 8th of a loaf"
* "a 3 foot stick" should result in "an 8 foot stick", not "a 8 foot stick"

So numbers of the following forms could have a special plural category and special ordinal category: 8(X), 11(X), 18(X), 8x(X), where x is 0..9 and the optional X is 00, 000, 00000, and so on.

On the other hand, the above constructions are relatively rare in messages constructed using numeric placeholders, so the disruption for implementations currently using CLDR plural categories wouldn't be worth the small gain.

This section defines the types of plural forms that exist in a language—namely, the cardinal and ordinal plural forms. Cardinal plural forms express units such as time, currency or distance, used in conjunction with a number expressed in decimal digits (i.e. "2", not "two", and not an indefinite number such as "some" or "many"). Ordinal plural forms denote the order of items in a set and are always integers. For example, English has two forms for cardinals:

* form "one": 1 day
* form "other": 0 days, 2 days, 10 days, 0.3 days

and four forms for ordinals:

* form "one": 1st floor, 21st floor, 101st floor
* form "two": 2nd floor, 22nd floor, 102nd floor
* form "few": 3rd floor, 23rd floor, 103rd floor
* form "other": 4th floor, 11th floor, 96th floor

Other languages may have additional forms or only one form for each type of plural. CLDR provides the following tags for designating the various plural forms of a language; for a given language, only the tags necessary for that language are defined, along with the specific numeric ranges covered by each tag (for example, the plural form "few" may be used for the numeric range 2–4 in one language and 3–9 in another):

* zero (see also plural case “0”, described in [Explicit 0 and 1 rules](#Explicit_0_1_rules))
* one (see also plural case “1”, described in [Explicit 0 and 1 rules](#Explicit_0_1_rules))
* two
* few
* many

In addition, an "other" tag is always implicitly defined to cover the forms not explicitly designated by the tags defined for a language. This "other" tag is also used for languages that only have a single form (in which case no plural-form tags are explicitly defined for the language). For a more complex example, consider the cardinal rules for Russian and certain other languages:

```xml
<pluralRules locales="hr ru sr uk">
    <pluralRules count="one">n mod 10 is 1 and n mod 100 is not 11</pluralRule>
    <pluralRules count="few">n mod 10 in 2..4 and n mod 100 not in 12..14</pluralRule>
</pluralRules>
```

These rules specify that Russian has a "one" form (for 1, 21, 31, 41, 51, …), a "few" form (for 2–4, 22–24, 32–34, …), and implicitly an "other" form (for everything else: 0, 5–20, 25–30, 35–40, …, decimals). Russian does not need additional separate forms for zero, two, or many, so these are not defined.

A source number represents the visual appearance of the digits of the result. In text, it can be represented by the EBNF for sampleValue. Note that the same double number can be represented by multiple source numbers. For example, "1.0" and "1.00" are different source numbers, but there is only one double number that they correspond to: 1.0d == 1.00d. As another example, 1e3d == 1000d, but the source numbers "1e3" and "1000" are different, and can have different plural categories. So the input to the plural rules carries more information than a computer double. The plural category for negative numbers is calculated according to the absolute value of the source number, and leading integer digits don't have any effect on the plural category calculation. (This may change in the future, if we find languages that have different behavior.)

Plural categories may also differ according to the visible decimals. For example, here are some of the behaviors exhibited by different languages:

| Behavior | Description                                                                         | Example                                                                |
|----------|-------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| Base     | The fractions are ignored; the category is the same as the category of the integer. | 1.13 has the same plural category as 1.                                |
| Separate | All fractions by value are in one category (typically ‘other’ = ‘plural’).          | 1.01 gets the same class as 9; <br/> 1.00 gets the same category as 1. |
| Visible  | All visible fractions are in one category (typically ‘other’ = ‘plural’).           | 1.00, 1.01, 3.5 all get the same category.                             |
| Digits   | The visible fraction determines the category.                                       | 1.13 gets the same class as 13.                                        |

There are also variants of the above: for example, short fractions may have the Digits behavior, but longer fractions may just look at the final digit of the fraction.

Currently there are no locale keywords that affect plural rule selection; they are selected using the base locale ID, ignoring any -u- extension keywords.

#### <a name="Explicit_0_1_rules" href="#Explicit_0_1_rules">Explicit 0 and 1 rules</a>

Some types of CLDR data (such as [unitPatterns](tr35-general.md#Unit_Elements) and [currency displayNames](#Currencies)) allow specification of plural rules for explicit cases “0” and “1”, in addition to the language-specific plural cases specified above: “zero”, “one”, “two” ... “other”. For the language-specific plural rules:

* The rules depend on language; for a given language, only a subset of the cases may be defined. For example, English only defines “one” and “other”, cases like “two” and “few” cannot be used in plurals for English CLDR items.
* Each plural case may cover multiple numeric values, and may depend on the formatting of those values. For example, in French the “one” case covers 0.0 through 1.99.
* The “one” case, if defined, includes at least some formatted forms of the numeric value 1; the “zero” case, if defined, includes at least some formatted forms of the numeric value 0.

By contrast, for the explicit cases “0” and “1”:

* The explicit “0” and “1” cases are not defined by language-specific rules, and are available in any language for the CLDR data items that accept them.
* The explicit “0” and “1” cases apply to the exact numeric values 0 and 1 respectively. These cases are typically used for plurals of items that do not have fractional value, like books or files.
* The explicit “0” and “1” cases have precedence over the “zero” and “one” cases. For example, if for a particular element CLDR data includes values for both the “1” and “one” cases, then the “1” value is used for numeric values of exactly 1, while the “one” value is used for any other formatted numeric values matching the “one” plural rule for the language.

Usage example: In English (which only defines language-specific rules for “one” and “other”) this can be used to have special behavior for 0:

* count=“0”: no books
* count=“one”: {0} book, e.g. “1 book”
* count=“other”: {0} books, e.g. “3 books”

### <a name="Plural_rules_syntax" href="#Plural_rules_syntax">Plural rules syntax</a>

The xml value for each pluralRule is a _condition_ with a boolean result.
That value specifies whether that rule (i.e. that plural form) applies to a given _source number N_ in sampleValue syntax, where _N_ can be expressed as a decimal fraction or with compact decimal formatting.
The compact decimal formatting is denoted by a special notation in the syntax, e.g., “1.2c6” for “1.2M”.
Clients of CLDR may express all the rules for a locale using the following syntax:

```
rules         = rule (';' rule)*
rule          = keyword ':' condition samples
              | 'other' ':' samples
keyword       = [a-z]+
keyword       = [a-z]+
```

In CLDR, the keyword is the attribute value of 'count'. Those values in CLDR are currently limited to just what is in the DTD, but clients may support other values.

The conditions themselves have the following syntax.

```
condition       = and_condition ('or' and_condition)*
samples         = ('@integer' sampleList)?
                  ('@decimal' sampleList)?
and_condition   = relation ('and' relation)*
relation        = is_relation | in_relation | within_relation
is_relation     = expr 'is' ('not')? value
in_relation     = expr (('not')? 'in' | '=' | '!=') range_list
within_relation = expr ('not')? 'within' range_list
expr            = operand (('mod' | '%') value)?
operand         = 'n' | 'i' | 'f' | 't' | 'v' | 'w' | 'c' | 'e'
range_list      = (range | value) (',' range_list)*
range           = value'..'value
value           = digit+
sampleList      = sampleRange (',' sampleRange)* (',' ('…'|'...'))?
sampleRange     = sampleValue ('~' sampleValue)?
sampleValue     = sign? value ('.' digit+)? ([ce] digitPos digit+)?
sign            = '+' | '-'
digit           = [0-9]
digitPos        = [1-9]
```

* Whitespace (defined as Unicode [Pattern_White_Space](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5Cp%7BPattern_White_Space%7D)) can occur between or around any of the above tokens, with the exception of the tokens in value, digit, and sampleValue.
* In the syntax, **and** binds more tightly than **or**. So **X or Y and Z** is interpreted as **(X or (Y and Z))**.
  * For example, e = 0 and i != 0 and i % 1000000 = 0 and *+v = 0+* or e != 0..5 is parsed as if it were (e = 0 and i != 0 and i % 1000000 = 0 and v = 0) or (e != 0..5)
* Each plural rule must be written to be self-contained, and not depend on the ordering. Thus rules must be mutually exclusive; for a given numeric value, only one rule can apply (i.e., the condition can only be true for one of the pluralRule elements). Each keyword can have at most one condition. The 'other' keyword must have an empty condition: it is only present for samples.
* The samples should be included, since they are used by client software for samples and determining whether the keyword has finite values or not.
* The 'other' keyword must have no condition, and all other keywords must have a condition.

#### <a name="Operands" href="#Operands">Operands</a>

The operands are numeric values corresponding to features of the *source number N*, and have the following meanings given in the table below.
Note that, contrary to source numbers, operands are treated numerically.
Although some of them are used to describe insignificant 0s in the source number, any insignificant 0s in the operands themselves are ignored, e.g., f=03 is equivalent to f=3.

###### Table: <a name="Plural_Operand_Meanings" href="#Plural_Operand_Meanings">Plural Operand Meanings</a>

| Symbol | Value                                                                                           |
|--------|-------------------------------------------------------------------------------------------------|
| n      | the absolute value of N.*                                                                       |
| i      | the integer digits of N.*                                                                       |
| v      | the number of visible fraction digits in N, _with_ trailing zeros.*                             |
| w      | the number of visible fraction digits in N, _without_ trailing zeros.*                          |
| f      | the visible fraction digits in N, _with_ trailing zeros, expressed as an integer.*              |
| t      | the visible fraction digits in N, _without_ trailing zeros, expressed as an integer.*           |
| c      | compact decimal exponent value: exponent of the power of 10 used in compact decimal formatting. |
| e      | a deprecated synonym for ‘c’. Note: it may be redefined in the future.                          |

\* If there is a compact decimal exponent value (‘c’), then the n, i, f, t, v, and w values are computed _after_ shifting the decimal point in the original by the ‘c’ value.
So for 1.2c3, the n, i, f, t, v, and w values are the same as those of 1200:  i=1200 and f=0.
Similarly, 1.2005c3 has i=1200 and f=5 (corresponding to 1200.5).

###### Table: <a name="Plural_Operand_Examples" href="#Plural_Operand_Examples">Plural Operand Examples</a>

|    source |         n |         i | v | w |   f |  t | e |
|----------:|----------:|----------:|--:|--:|----:|---:|--:|
|         1 |         1 |         1 | 0 | 0 |   0 |  0 | 0 |
|       1.0 |         1 |         1 | 1 | 0 |   0 |  0 | 0 |
|      1.00 |         1 |         1 | 2 | 0 |   0 |  0 | 0 |
|       1.3 |       1.3 |         1 | 1 | 1 |   3 |  3 | 0 |
|      1.30 |       1.3 |         1 | 2 | 1 |  30 |  3 | 0 |
|      1.03 |      1.03 |         1 | 2 | 2 |   3 |  3 | 0 |
|     1.230 |      1.23 |         1 | 3 | 2 | 230 | 23 | 0 |
|   1200000 |   1200000 |   1200000 | 0 | 0 |   0 |  0 | 0 |
|     1.2c6 |   1200000 |   1200000 | 0 | 0 |   0 |  0 | 6 |
|     123c6 | 123000000 | 123000000 | 0 | 0 |   0 |  0 | 6 |
|     123c5 |  12300000 |  12300000 | 0 | 0 |   0 |  0 | 5 |
|   1200.50 |    1200.5 |      1200 | 2 | 1 |  50 |  5 | 0 |
| 1.20050c3 |    1200.5 |      1200 | 2 | 1 |  50 |  5 | 3 |


#### <a name="Relations" href="#Relations">Relations</a>

The positive relations are of the format **x = y** and **x = y mod z**. The **y** value can be a comma-separated list, such as **n = 3, 5, 7..15**, and is treated as if each relation were expanded into an OR statement. The range value **a..b** is equivalent to listing all the ***integers*** between **a** and **b**, inclusive. When **!=** is used, it means the entire relation is negated.

###### Table: <a name="Relations_Examples" href="#Relations_Examples">Relations Examples</a>

| Expression    | Meaning                                 |
|---------------|-----------------------------------------|
| x = 2..4, 15  | x = 2 OR x = 3 OR x = 4 OR x = 15       |
| x != 2..4, 15 | NOT (x = 2 OR x = 3 OR x = 4 OR x = 15) |

| Expression      | Value |
|-----------------|-------|
| 3.5 = 2..4, 15  | false |
| 3.5 != 2..4, 15 | true  |
| 3 = 2..4, 15    | true  |
| 3 != 2..4, 15   | false |

> The old keywords 'mod', 'in', 'is', and 'within' are present only for backwards compatibility. The preferred form is to use '%' for modulo, and '=' or '!=' for the relations, with the operand 'i' instead of within. (The difference between **in** and **within** is that **in** only includes integers in the specified range, while **within** includes all values.)

The modulus (% or **mod**) is a remainder operation as defined in Java; for example, where **n** = 4.3 the result of **n mod 3** is 1.3.

The values of relations are defined according to the operand as follows. Importantly, the results may depend on the visible decimals in the source, including trailing zeros, and the compact decimal exponent.

1. Let the base value BV be computed from absolute value of the original source number according to the operand.
2. Let R be false when the comparison contains ‘not’.
3. Let R be !R if the comparison contains ‘within’ and the source number is not an integer.
4. If there is a module value MV, let BV be BV - floor(BV/MV).
5. Let CR be the list of comparison ranges, normalized that overlapping ranges are merged. Single values in the rule are represented by a range with identical \<starti, endi> values.
6. Iterate through CR:
   * if starti ≤ BV ≤ endi then return R.
7. Otherwise return !R.

###### Table: <a name="Plural_Rules_Examples" href="#Plural_Rules_Examples">Plural Rules Examples</a>

| Rules | Comments |
| --- | --- |
| one: n = 1 <br/> few: n = 2..4 | This defines two rules, for 'one' and 'few'. The condition for 'one' is "n = 1" which means that the number must be equal to 1 for this condition to pass. The condition for 'few' is "n = 2..4" which means that the number must be between 2 and 4 inclusive for this condition to pass. All other numbers are assigned the keyword 'other' by the default rule. |
| zero: n = 0 or n != 1 and n mod 100 = 1..19 <br/> one: n = 1 | Each rule must not overlap with other rules. Also note that a modulus is applied to n in the last rule, thus its condition holds for 119, 219, 319, … |
| one: n = 1 <br/> few: n mod 10 = 2..4 and n mod 100 != 12..14 | This illustrates conjunction and negation. The condition for 'few' has two parts, both of which must be met: "n mod 10 = 2..4" and "n mod 100 != 12..14". The first part applies a modulus to n before the test as in the previous example. The second part applies a different modulus and also uses negation, thus it matches all numbers _not_ in 12, 13, 14, 112, 113, 114, 212, 213, 214, … |

#### <a name="Samples" href="#Samples">Samples</a>

Samples are provided if sample indicator (@integer or @decimal) is present on any rule. (CLDR always provides samples.)

Where samples are provided, the absence of one of the sample indicators indicates that no numeric values can satisfy that rule. For example, the rule "i = 1 and v = 0" can only have integer samples, so @decimal must not occur. The @integer samples have no visible fraction digits, while @decimal samples have visible fraction digits; both can have compact decimal exponent values (if the 'e' operand occurs).

The sampleRanges have a special notation: **start**~**end**. The **start** and **end** values must have the same number of decimal digits, and the same compact decimal exponent values (or neither have compact decimal exponent values). The range encompasses all and only those values **v** where **start ≤ v ≤ end**, and where **v** has the same number of decimal places as **start** and **end**, and the same compact decimal exponent values.

Samples must indicate whether they are infinite or not. The '…' marker must be present if and only if infinitely many values (integer or decimal) can satisfy the rule. If a set is not infinite, it must list all the possible values.

###### Table: <a name="Plural_Samples_Examples" href="#Plural_Samples_Examples">Plural Samples Examples</a>

| Rules | Comments |
| --- | --- |
| @integer 1, 3~5 | 1, 3, 4, 5. |
| @integer 3\~5, 103\~105, … | Infinite set: 3, 4, 5, 103, 104, 105, … |
| @decimal 1.3\~1.5, 1.03\~1.05, … | Infinite set: 1.3, 1.4, 1.5, 1.03, 1.04, 1.05, … |

In determining whether a set of samples is infinite, leading zero integer digits and trailing zero decimals are not significant. Thus "i = 1000 and f = 0" is satisfied by 01000, 1000, 1000.0, 1000.00, 1000.000, 01c3 etc. but is still considered finite.

#### <a name="Using_cardinals" href="#Using_cardinals">Using Cardinals</a>

Elements such as `<currencyFormats>`, `<currency>` and `<unit>` provide selection among subelements designating various localized cardinal plural forms by tagging each of the relevant subelements with a different count value, or with no count value in some cases. Note that the plural forms for a specific currencyFormat, unit type, or currency type may not use all of the different plural-form tags defined for the language. To format a currency or unit type for a particular numeric value, determine the count value according to the plural rules for the language, then select the appropriate display form for the currency format, currency type or unit type using the rules in those sections:

* 2.3 [Number Symbols](#Number_Symbols) (for `currencyFormat`s elements)
* [Currencies](#Currencies) (for `currency` elements)
* The main document [Unit Elements](tr35.md#Unit_Elements)

### <a name="Plural_Ranges" href="#Plural_Ranges">Plural Ranges</a>

```dtd
<!ELEMENT pluralRanges (pluralRange*) >
<!ATTLIST pluralRanges locales NMTOKENS #REQUIRED >

<!ELEMENT pluralRange ( #PCDATA ) >
<!ATTLIST pluralRange start (zero|one|two|few|many|other) #IMPLIED >
<!ATTLIST pluralRange end (zero|one|two|few|many|other) #IMPLIED >
<!ATTLIST pluralRange result (zero|one|two|few|many|other) #REQUIRED >
```

Often ranges of numbers are presented to users, such as in “Length: 3.2–4.5 centimeters”. This means any length from 3.2 cm to 4.5 cm, inclusive. However, different languages have different conventions for the pluralization given to a range: should it be “0–1 centimeter” or “0–1 centimeters”? This becomes much more complicated for languages that have many different plural forms, such as Russian or Arabic.

The `pluralRanges` element provides information allowing an implementation to derive the plural category of a range from the plural categories of the `start` and `end` values. If there is no value for a _<`start`,`end`>_ pair, the default result is `end`. However, where that result has been verified for a given language, it is included in the CLDR data.

The data has been gathered presuming that in any usage, the start value is strictly less than the end value, and that no values are negative. Results for any cases that do not meet these criteria are undefined.

For the formatting of number ranges, see <a href="#Number_Range_Formatting">Number Range Formatting</a>.

## <a name="Rule-Based_Number_Formatting" href="#Rule-Based_Number_Formatting">Rule-Based Number Formatting</a>

```dtd
<!ELEMENT rbnf ( alias | rulesetGrouping*) >

<!ELEMENT rulesetGrouping ( alias | rbnfRules? ruleset*) >
<!ATTLIST rulesetGrouping type NMTOKEN #REQUIRED>

<!ELEMENT rbnfRules ( #PCDATA )>

<!ELEMENT ruleset ( alias | rbnfrule*) >
<!ATTLIST ruleset type NMTOKEN #REQUIRED>
<!ATTLIST ruleset access ( public | private ) #IMPLIED >

<!ELEMENT rbnfrule ( #PCDATA ) >
<!ATTLIST rbnfrule value CDATA #REQUIRED >
<!ATTLIST rbnfrule radix CDATA #IMPLIED >
<!ATTLIST rbnfrule decexp CDATA #IMPLIED >
```

The rule-based number format (RBNF) encapsulates a set of rules for transforming numeric values to and from a representation words that represent a number. For example, format 25,376 as "twenty-five thousand three hundred seventy-six" or "vingt-cinq mille trois cent soixante-seize" or "fünf­und­zwanzig­tausend­drei­hundert­sechs­und­siebzig" depending on the language being used. These rules are typically used for spelling out numeric values, but can also be used for other number systems like roman numerals, Chinese numerals, or for ordinal numbers with digits (e.g. 1st, 2nd, 3rd, …).

Where, however, the [[CLDR plurals or ordinals]](#language-plural-rules) can be used, their usage is recommended in preference to the RBNF data. First, the RBNF data may be missing some relevant forms for grammatical case or types over some languages that otherwise have modern coverage. Secondly, the choice of rules requires additional language specific context. CLDR does not supply any data for when to use one vs another (e.g. when to use `spellout-cardinal-masculine` vs `spellout-cardinal-feminine`). So these data are insufficient without additional software that provides grammatical context to choose the correct rule for grammatical agreement in a sentence.

### <a name="Rule-Based_Number_Formatting_Scope" href="#Rule-Based_Number_Formatting_Scope">Rule-Based Number Formatting Scope</a>

```xml
<ruleSetGrouping>
```

Used to group rules into functional sets. There are 3 known rule types. They are `SpelloutRules`, `NumberingSystemRules`, and `OrdinalRules`.

#### <a name="SpelloutRules" href="#SpelloutRules">SpelloutRules</a>

The `SpelloutRules` type is used for representing a numerical datatype with words that are typically found in speech. There are 4 common rule categories for spellout rules. A language with complete coverage will cover all of these categories. Some languages may provide more than these 4 types depending on what is relevant for a given language:

##### numbering 
This is the default used when there is no context for the number. Usually this is used for counting without reference to a noun. For many languages, this may also be used for enumeration of objects, like used when pronouncing "table number one" and "table number two". It can also be used for pronouncing a math equation, like "2 - 3 = -1".
##### numbering-year
This is used for cases where years are pronounced or written a certain way. An example in English is the year 1999, which comes out as "nineteen ninety-nine" instead of the numbering value "one thousand nine hundred ninety-nine". The rules for this type have undefined behavior for non-integer numbers, and values less than 1.
##### cardinal
This is used when providing the quantity of the number of objects. For many languages, there may not be a default cardinal type. Many languages require the notion of the gender and other grammatical properties so that the number and the objects being referenced are in grammatical agreement. An example of its usage is "one e-mail", "two people" or "three kilometers". Some languages may not have dedicated words for 0 or negative numbers for cardinals. In those cases, the words from the numbering type can be reused.
##### ordinal
This is used when providing the order of the number of objects. For many languages, there may not be a default ordinal type. Many languages also require the notion of the gender for ordinal so that the ordinal number and the objects being referenced are in grammatical agreement. An example of its usage is "first place", "second e-mail" or "third house on the right". The rules for this type have undefined behavior for non-integer numbers, and values less than 1.

#### <a name="NumberingSystemRules" href="#NumberingSystemRules">NumberingSystemRules</a>

The `NumberingSystemRules` type is used for numbering systems. Even though they may be derived from a specific culture, they are typically not translated and the rules are in **root**. An example of these rules are the Roman numerals where the value 8 comes out as VIII. These are typically supported in scenarios where the numbering system does not use decimal digits, but symbols are used to represent a number. 

#### <a name="OrdinalRules" href="#OrdinalRules">OrdinalRules</a>

The `OrdinalRules` type is used for ordinal numbers with digits (e.g. 1st, 2nd, 3rd, …). If a language does not have such a concept, then it should be the format of numbers in an ordered list.

### <a name="RBNF_Limitations" href="#RBNF_Limitations">Limitations</a>

With regards to the number range supported for all these number types, the largest possible number range tries to be supported, but some languages may not have words for large numbers. For example, the old Roman numbering system can't support the value 5000 and beyond. For those unsupported cases, the default number format from CLDR is used.

For most languages, the largest number represented by the number rules in CLDR is typically 1 quintillion - 1 or 10<sup>18</sup> - 1. Some languages may not have commonly recognized words for these large numbers. These larger values are also hard to represent accurately in an IEEE 754 double floating point number, which only has 53 bits of precision. A signed 64-bit number can only represent 9.2 quintillion. Even if larger values were supported, the value of such translations would be limited.

Number types that have a small limited range are generally not supported by these number rules. For example, multiplicatives (e.g. single, double, triple) could be supported, but the range is so small that the entirety of well known words could be represented with a small lookup table. There is no recursion needed to support such rules. Due to their limited nature with a narrow range, such rules are generally not supported.

Numbers with units are inappropriate to use within these number rules. It's more appropriate to format or parse the number component, and to use a message format framework to format the unit. This number rules based system does not scale well with the number of combinations of possible values with units and subunits. Such implementations will also struggle with adjusting the precision for formatting or the relevant unit recognized when parsing. It is more scalable to use this number rules with a [[Language Plural Rules]](#language-plural-rules) with a message formatting framework.

### <a name="RBNF_Syntax" href="#RBNF_Syntax">Syntax of `rbnfRules`</a>
```xml
<rbnfRules>
```

The syntax is carried over from the ICU based RBNF rules. The rules are fairly sophisticated. For more details see [_Rule-Based Number Formatter_](tr35.md#RBNF).

In its simplest form, the description consists of a semicolon-delimited list of *rules*.
Each rule has a string of output text and a value or range of values it is applicable to.
In a typical spellout rule set, the first twenty rules are the words for the numbers from
0 to 19:

```
zero; one; two; three; four; five; six; seven; eight; nine;
ten; eleven; twelve; thirteen; fourteen; fifteen; sixteen; seventeen; eighteen; nineteen;
```

For larger numbers, we can use the preceding set of rules to format the ones place, and
we only have to supply the words for the multiples of 10:

```
20: twenty[->>];
30: thirty[->>];
40: forty[->>];
50: fifty[->>];
60: sixty[->>];
70: seventy[->>];
80: eighty[->>];
90: ninety[->>];
```

In these rules, the *base value* is spelled out explicitly and set off from the
rule's output text with a colon. The rules are in a sorted list, and a rule is applicable
to all numbers from its own base value to one less than the next rule's base value. The
">>" token is called a *substitution* and tells the formatter to
isolate the number's ones digit, format it using this same set of rules, and place the
result at the position of the ">>" token. Text in brackets is omitted if
the number being formatted is an even multiple of 10 (the hyphen is a literal hyphen; 24
is "twenty-four," not "twenty four").

For even larger numbers, we can actually look up several parts of the number in the
list:

```
100: << hundred[ >>];
```

The "<<" represents a new kind of substitution. The << isolates
the hundreds digit (and any digits to its left), formats it using this same rule set, and
places the result where the "<<" was. Notice also that the meaning of >>
has changed: it now refers to both the tens and the ones digits. The meaning of
both substitutions depends on the rule's base value. The base value determines the rule's *divisor*,
which is the highest power of 10 that is less than or equal to the base value (the user
can change this). To fill in the substitutions, the formatter divides the number being
formatted by the divisor. The integral quotient is used to fill in the <<
substitution, and the remainder is used to fill in the >> substitution. The meaning
of the brackets changes similarly: text in brackets is omitted if the value being
formatted is an even multiple of the rule's divisor. The rules are applied recursively, so
if a substitution is filled in with text that includes another substitution, that
substitution is also filled in.

This rule covers values up to 999, at which point we add another rule:

```
1000: << thousand[ >>];
```

Just like the 100 rule, the meanings of the brackets and substitution tokens shift because the rule's
base value is a higher power of 10, changing the rule's divisor. This rule can actually be
used all the way up to 999,999. This allows us to finish out the rules as follows:

```
1,000,000: << million[ >>];
1,000,000,000: << billion[ >>];
1,000,000,000,000: << trillion[ >>];
1,000,000,000,000,000: =#,##0=;
```

Commas, periods, and spaces can be used in the base values to improve legibility and
are ignored by the rule parser. The last rule in the list is customarily treated as an
"overflow rule", which applies to everything from its base value on up.
It is often used to print out a default representation, which in this case is the decimal format syntax.

To see how these rules actually work in practice, consider the following example.
Formatting 25,340 with this rule set would work like this:

| Rule                                         | Description                                                                                                                         |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| **<< thousand >>**                           | The rule whose base value is 1,000 is applicable to 25,340                                                                          |
| **twenty->>** thousand >>                    | 25,340 over 1,000 is 25. The rule for 20 applies.                                                                                   |
| twenty-**five** thousand >>                  | 25 mod 10 is 5. The rule for 5 is "five."                                                                                           |
| twenty-five thousand **<< hundred >>**       | 25,340 mod 1,000 is 340. The rule for 100 applies.                                                                                  |
| twenty-five thousand **three** hundred >>    | 340 over 100 is 3. The rule for 3 is "three."                                                                                       |
| twenty-five thousand three hundred **forty** | 340 mod 100 is 40. The rule for 40 applies. Since 40 divides evenly by 10, the hyphen and substitution in the brackets are omitted. |

The above syntax suffices only to format positive integers. To format negative numbers, we add a special rule:

```
-x: minus >>;
```

This is called a *negative-number rule*, and is identified by "-x"
where the base value would be. This rule is used to format all negative numbers. the >>
token here means "find the number's absolute value, format it with these
rules, and put the result here."

We also add a special rule called a *fraction rule* for numbers with fractional parts:

```
x.x: << point >>;
```

This rule is used for all positive non-integers (negative non-integers pass through the
negative-number rule first and then through this rule). Here, the << token refers to
the number's integral part, and the >> to the number's fractional part. The
fractional part is formatted as a series of single-digit numbers (e.g., 123.456 would be
formatted as "one hundred twenty-three point four five six").

### <a name="RBNF_Syntax_Rule_Set" href="#RBNF_Syntax_Rule_Set">Rule Sets</a>

Multiple sets of rules can be defined with one or more *rule
sets*. Each rule set consists of a name, a colon, and a list of *rules*. A rule
set name must begin with a % sign. Rule sets with a name that begins with a single % sign
are *public*, and that name can be referenced to format and parse numbers.
Rule sets with names that begin with %% are *private*. They exist only for the use
by other rule sets. If a formatter only has one rule set, the name may be omitted.

To improve parsing of numbers, a special "rule set" named `%%lenient-parse` can be used.
The body of `%%lenient-parse` isn't a set of number-formatting rules. It is a set of [collation rules](tr35-collation.md).
These rules define equivalences for lenient parsing. Symbols that have syntactic meaning
in collation rules, such as '&', have no particular meaning when appearing outside
of the `lenient-parse` rule set.

The body of a rule set consists of an ordered, semicolon-delimited list of *rules*.
Internally, every rule has a base value, a divisor, rule text, and zero, one, or two *substitutions*.
These parameters are controlled by the description syntax, which consists of a *rule
descriptor*, a colon, and a *rule body*.

A rule descriptor can take one of the following forms. The text in *italics* is the
name of a token.

| Descriptor   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *bv*:        | *bv* specifies the rule's base value. *bv* is a decimal number expressed using ASCII digits. *bv* may contain spaces, period, and commas, which are ignored. The rule's divisor is the highest power of 10 less than or equal to the base value.                                                                                                                                                                                                                                                                                                                           |
| *bv*/*rad*:  | *bv* specifies the rule's base value. The rule's divisor is the highest power of *rad* less than or equal to the base value.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| *bv*>:       | *bv* specifies the rule's base value. To calculate the divisor, let the radix be 10, and the exponent be the highest exponent of the radix that yields a result less than or equal to the base value. Every > character after the base value decreases the exponent by 1. If the exponent is positive or 0, the divisor is the radix raised to the power of the exponent; otherwise, the divisor is 1.                                                                                                                                                                     |
| *bv*/*rad*>: | *bv* specifies the rule's base value. To calculate the divisor, let the radix be *rad*, and the exponent be the highest exponent of the radix that yields a result less than or equal to the base value. Every > character after the radix decreases the exponent by 1. If the exponent is positive or 0, the divisor is the radix raised to the power of the exponent; otherwise, the divisor is 1.                                                                                                                                                                       |
| -x:          | The rule is a negative-number rule.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| x.x          | The rule is an *improper fraction rule*. If the full stop in the middle of the rule name is replaced with the decimal point that is used in the language or a [Decimal Format Pattern](#Number_Format_Patterns), then that rule will have precedence when formatting and parsing this rule. For example, some languages use the comma, and can thus be written as x,x instead. For example, you can use "x.x: << point >>;x,x: << comma >>;" to handle the decimal point that matches the language's natural spelling of the punctuation of either the full stop or comma. |
| 0.x:         | The rule is a *proper fraction rule*. If the full stop in the middle of the rule name is replaced with the decimal point that is used in the language or [Decimal Format Pattern](#Number_Format_Patterns), then that rule will have precedence when formatting and parsing this rule. For example, some languages use the comma, and can thus be written as 0,x instead. For example, you can use "0.x: point >>;0,x: comma >>;" to handle the decimal point that matches the language's natural spelling of the punctuation of either the full stop or comma.            |
| x.0:         | The rule is a *default rule*. If the full stop in the middle of the rule name is replaced with the decimal point that is used in the language or [Decimal Format Pattern](#Number_Format_Patterns), then that rule will have precedence when formatting and parsing this rule. For example, some languages use the comma, and can thus be written as x,0 instead. For example, you can use "x.0: << point;x,0: << comma;" to handle the decimal point that matches the language's natural spelling of the punctuation of either the full stop or comma.                    |
| Inf:         | The rule for infinity.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| NaN:         | The rule for an IEEE 754 NaN (not a number).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| *nothing*    | If the rule's rule descriptor is left out, the base value is one plus the preceding rule's base value (or zero if this is the first rule in the list) in a normal rule set. In a fraction rule set, the base value is the same as the preceding rule's base value.                                                                                                                                                                                                                                                                                                         |

A rule set may be either a regular rule set or a *fraction rule set*, depending
on whether it is used to format a number's integral part (or the whole number) or a
number's fractional part. Using a rule set to format a rule's fractional part makes it a
fraction rule set.

Which rule is used to format a number is defined according to one of the following
algorithms. If the rule set is a regular rule set, do the following:

* If the rule set includes a default rule (and the number was passed in as a `double`),
    use the default rule. If the number being formatted was passed in as a `long`,
    the default rule is ignored.
* If the number is negative, use the negative-number rule.
* If the number has a fractional part and is greater than 1, use the improper fraction rule.
* If the number has a fractional part and is between 0 and 1, use the proper fraction rule.
* Binary-search the rule list for the rule with the highest base value less than or equal
    to the number. If that rule has two substitutions, its base value is not an even multiple
    of its divisor, and the number *is* an even multiple of the rule's divisor, use the
    rule that precedes it in the rule list. Otherwise, use the rule itself.

If the rule set is a fraction rule set, do the following:

* Ignore negative-number and fraction rules.
* For each rule in the list, multiply the number being formatted (which will always be
    between 0 and 1) by the rule's base value. Keep track of the distance between the result
    the nearest integer.
* Use the rule that produced the result closest to zero in the above calculation. In the
    event of a tie or a direct hit, use the first matching rule encountered. The idea here is
    to try each rule's base value as a possible denominator of a fraction. Whichever
    denominator produces the fraction closest in value to the number being formatted wins. If
    the rule following the matching rule has the same base value, use it if the numerator of
    the fraction is anything other than 1; if the numerator is 1, use the original matching
    rule. This is to allow singular and plural forms of the rule text without a lot of extra
    hassle.

A rule's body consists of a string of characters terminated by a semicolon. The rule
may include zero, one, or two *substitution tokens*, and a range of text in
brackets. The brackets denote optional text (and may also include one or both
substitutions). The exact meanings of the substitution tokens, and under what conditions
optional text is omitted, depend on the syntax of the substitution token and the context.
The rest of the text in a rule body is literal text that is output when the rule matches
the number being formatted.

A substitution token begins and ends with a *token character*. The token
character and the context together specify a mathematical operation to be performed on the
number being formatted. An optional *substitution descriptor* specifies how the
value resulting from that operation is used to fill in the substitution. The position of
the substitution token in the rule body specifies the location of the resultant text in
the original rule text.

The meanings of the substitution token characters are as follows:

| Syntax                         | Context                      | Usage                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|--------------------------------|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \>>                            | in normal rule               | Divide the number by the rule's divisor and format the remainder.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| \>>                            | in negative-number rule      | Find the absolute value of the number and format the result.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| \>>                            | in fraction or default rule  | Isolate the number's fractional part and format it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| \>>                            | in rule in fraction rule set | Not allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| \>>>                           | in normal rule               | Divide the number by the rule's divisor and format the remainder, but bypass the normal rule-selection process and just use the rule that precedes this one in this rule list.                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| \>>>                           | in all other rules           | Not allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| <<                             | in normal rule               | Divide the number by the rule's divisor, perform floor() on the quotient, and format the resulting value.<br> If there is a [Decimal Format Pattern](#Number_Format_Patterns) between the < characters and the rule does NOT also contain a >> substitution, we don't perform floor() on the quotient. The quotient is passed through to the [Decimal Format Pattern](#Number_Format_Patterns) intact.  That is, for the value 1,900:<ul><li>"1/1000: << thousand;" will produce "one thousand"</li><li>"1/1000: <0< thousand;" will produce "2 thousand" (*not* "1 thousand")</li><li>"1/1000: <0< thousand >0>;" will produce "1 thousand 900"</ul> |
| <<                             | in negative-number rule      | Not allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| <<                             | in fraction or default rule  | Isolate the number's integral part and format it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| <<                             | in rule in fraction rule set | Multiply the number by the rule's base value and format the result.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ==                             | in all rule sets             | Format the number unchanged                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| []<br/>[\|]                    | in normal rule               | <ul><li>When the number is not an even multiple of the rule's divisor, use the text and rules between the beginning square bracket, and the end square bracket or the \| symbol.</li> <li>When the number is an even multiple of the rule's divisor, and no \| symbol is used, omit the text.</li> <li>When the number is an even multiple of the rule's divisor, and \| symbol is used, use the text and rules between the \| symbol, and the end square bracket.</li></ul>                                                                                                                                                                          |
| []<br/>[\|]                    | in improper-fraction rule    | This syntax is the same as specifying both an x.x rule and a 0.x rule. <ul><li>When the number is not between 0 and 1, use the text and rules between the beginning square bracket, and the end square bracket or the \| symbol.</li><li>When the number is between 0 and 1, and no \| symbol is used, omit the text.</li><li>When the number is between 0 and 1, and \| symbol is used, use the text and rules between the \| symbol, and the end square bracket.</li></ul>                                                                                                                                                                          |
| []<br/>[\|]                    | in default rule              | This syntax is the same as specifying both an x.x rule and an x.0 rule. <ul><li>When the number is not an integer, use the text and rules between the beginning square bracket, and the end square bracket or the \| symbol.</li> <li>When the number is an integer, and no \| symbol is used, omit the text.</li> <li>When the number is an integer, and \| symbol is used, use the text and rules between the \| symbol, and the end square bracket.</li></ul>                                                                                                                                                                                      |
| []<br/>[\|]                    | in rule in fraction rule set | <ul><li>When multiplying the number by the rule's base value does not yield 1, use the text and rules between the beginning square bracket, and the end square bracket or the \| symbol.</li> <li>When multiplying the number by the rule's base value yields 1, and no \| symbol is used, omit the text.</li> <li>When multiplying the number by the rule's base value yields 1, and \| symbol is used, use the text and rules between the \| symbol, and the end square bracket.</li></ul>                                                                                                                                                          |
| []<br/>[\|]                    | in proper-fraction rule      | Not allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| []<br/>[\|]                    | in negative-number rule      | Not allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| \$(cardinal,*plural syntax*)\$ | in all rule sets             | This provides the ability to choose a word based on the number divided by the radix to the power of the exponent of the base value for the specified locale, which is normally equivalent to the << value. This uses the cardinal plural rules from [[Language Plural Rules]](#language-plural-rules). All strings used in the plural format are treated as the same base value for parsing.                                                                                                                                                                                                                                                          |
| \$(ordinal,*plural syntax*)\$  | in all rule sets             | This provides the ability to choose a word based on the number divided by the radix to the power of the exponent of the base value for the specified locale, which is normally equivalent to the << value. This uses the ordinal plural rules from [[Language Plural Rules]](#language-plural-rules). All strings used in the plural format are treated as the same base value for parsing.                                                                                                                                                                                                                                                           |

The substitution descriptor (i.e., the text between the token characters) may take one of three forms:

| Descriptor                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|-----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| a rule set name                                     | Perform the mathematical operation on the number, and format the result using the named rule set.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| a [Decimal Format Pattern](#Number_Format_Patterns) | Perform the mathematical operation on the number, and format the result using a [Decimal Format Pattern](#Number_Format_Patterns) with the specified pattern. The pattern must begin with 0 or #.                                                                                                                                                                                                                                                                                                                                                                                  |
| nothing                                             | Perform the mathematical operation on the number, and format the result using the rule set containing the current rule, except: <ul><li>You can't have an empty substitution descriptor with a == substitution.</li> <li>If you omit the substitution descriptor in a >> substitution in a fraction rule, format the result one digit at a time using the rule set containing the current rule.</li> <li>If you omit the substitution descriptor in a << substitution in a rule in a fraction rule set, format the result using the default rule set for this formatter.</li></ul> |

Whitespace is ignored between a rule set name and a rule set body, between a rule
descriptor and a rule body, or between rules. If a rule body begins with an apostrophe,
the apostrophe is ignored, but all text after it becomes significant (this is how you can
have a rule's rule text begin with whitespace). There is no escape function: the semicolon
is not allowed in rule set names or in rule text, and the colon is not allowed in rule set
names. The characters beginning a substitution token are always treated as the beginning
of a substitution token.

### <a name="RBNF_Remove_Ruleset_Rule" href="#RBNF_Remove_Ruleset_Rule">Planned removal of ruleset and rule tags</a>

The following `<ruleset>` and `<rule>` tags will be removed in the next release. They contain redundant information contained in `<rbnfRules>` to provide time to transition to `<rbnfRules>`. 

```xml
<ruleset>
```

This element denotes a specific rule set to the number formatter. The ruleset is assumed to be a public ruleset unless the attribute type="private" is specified.

```xml
<rule>
```

Contains the actual formatting rule for a particular number or sequence of numbers. The `value` attribute is used to indicate the starting number to which the rule applies. The actual text of the rule is identical to the ICU syntax, with the exception that Unicode left and right arrow characters are used to replace < and > in the rule text, since < and > are reserved characters in XML. The `radix` attribute is used to indicate an alternate radix to be used in calculating the prefix and postfix values for number formatting. Alternate radix values are typically used for formatting year numbers in formal documents, such as "nineteen hundred seventy-six" instead of "one thousand nine hundred seventy-six".

## <a name="Parsing_Numbers" href="#Parsing_Numbers">Parsing Numbers</a>

The following elements are relevant to determining the value of a parsed number:

* A possible prefix or suffix, indicating sign
* A possible currency symbol or code
* Decimal digits
* A possible decimal separator
* A possible exponent
* A possible percent or per mille character

Other characters should either be ignored, or indicate the end of input, depending on the application. The key point is to disambiguate the sets of characters that might serve in more than one position, based on context. For example, a period might be either the decimal separator, or part of a currency symbol (for example, "NA f."). Similarly, an "E" could be an exponent indicator, or a currency symbol (the Swaziland Lilangeni uses "E" in the "en" locale). An apostrophe might be the decimal separator, or might be the grouping separator.

Here is a set of heuristic rules that may be helpful:

* Any character with the decimal digit property is unambiguous and should be accepted.

  **Note:** In some environments, applications may independently wish to restrict the decimal digit set to prevent security problems. See [[UTR36](https://www.unicode.org/reports/tr41/#UTR36)].

* The exponent character can only be interpreted as such if it occurs after at least one digit, and if it is followed by at least one digit, with only an optional sign in between. A regular expression may be helpful here.
* For the sign, decimal separator, percent, and per mille, use a set of all possible characters that can serve those functions. For example, the decimal separator set could include all of [.,']. (The actual set of characters can be derived from the number symbols in the By-Type charts [[ByType](tr35.md#ByType)], which list all of the values in CLDR.) To disambiguate, the decimal separator for the locale must be removed from the "ignore" set, and the grouping separator for the locale must be removed from the decimal separator set. The same principle applies to all sets and symbols: any symbol must appear in at most one set.
* Since there are a wide variety of currency symbols and codes, this should be tried before the less ambiguous elements. It may be helpful to develop a set of characters that can appear in a symbol or code, based on the currency symbols in the locale.
* Otherwise, a character should be ignored unless it is in the "stop" set. This includes even characters that are meaningful for formatting, for example, the grouping separator.
* If more than one sign, currency symbol, exponent, or percent/per mille occurs in the input, the first found should be used.
* A currency symbol in the input should be interpreted as the longest match found in the set of possible currency symbols.
* Especially in cases of ambiguity, the user's input should be echoed back, properly formatted according to the locale, before it is actually used for anything.

## <a name="Number_Range_Formatting" href="#Number_Range_Formatting">Number Range Formatting</a>

Often ranges of numbers are presented to users, such as in “Length: 3.2–4.5 centimeters”. This means any length from 3.2 cm to 4.5 cm, inclusive.

To format a number range, the following steps are taken:

1. Format the lower bound and the upper bound independently following the steps in [Number Format Patterns](#Number_Format_Patterns), preserving semantic annotations\*.
1. If the resulting values are identical, stop evaluating these steps and, instead, perform the steps in [Approximate Number Formatting](#Approximate_Number_Formatting).
    1. Note: This behavior may be customized in order to, for example, print the range despite the endpoints being identical. However, a spec-compliant implementation must support approximate number formatting.
1. Perform the steps in [Collapsing Number Ranges](#Collapsing_Number_Ranges), obtaining modified *lower* and *upper* values.
1. Obtain a number range pattern by following the steps in [Range Pattern Processing](#Range_Pattern_Processing).
1. Substitute *lower* as `{0}` and *upper* as `{1}` into the range pattern from the previous step.

\* Semantic annotations are discussed in [Collapsing Number Ranges](#Collapsing_Number_Ranges).

For plural rule selection of number ranges, see [Plural Ranges](#Plural_Ranges).

### <a name="Approximate_Number_Formatting" href="#Approximate_Number_Formatting">Approximate Number Formatting</a>

*Approximate number formatting* refers to a specific format of numbers in which the value is understood to not be exact; for example, "\~5 minutes".

To format an approximate number, follow the normal number formatting procedure in [Number Format Patterns](#Number_Format_Patterns), but substitute the `approximatelySign` from [Number Symbols](#Number_Symbols) in for the minus sign placeholder.

If the number is negative, or if the formatting options request the sign to be displayed, *prepend* the `approximatelySign` to the plus or minus sign before substituting it into the pattern. For example, "\~-5" means "approximately negative five". This procedure may change in the future.

### <a name="Collapsing_Number_Ranges" href="#Collapsing_Number_Ranges">Collapsing Number Ranges</a>

*Collapsing* a number range refers to the process of removing duplicated information in the *lower* and *upper* values. For example, if the lower string is "3.2 centimeters" and the upper string is "4.5 centimeters", it is desirable to remove the extra "centimeters" token.

This operation requires *semantic annotations* on the formatted value. The exact form of the semantic annotations is implementation-dependent. However, implementations may consider the following broad categories of tokens:

1. Numerical value, including decimal and grouping separators
1. Sign symbol
1. Scientific or compact notation
1. Unit of measurement

For example, consider the string `-5.3M US dollars`. It may be annotated as follows:

- `-` → sign symbol
- `5.3` → numerical value
- `M` → compact notation
- `US dollars` → unit of measurement for the currency USD

Two tokens are *semantically equivalent* if they have the same *semantic annotations*, even if they are not the exact same string. For example:

1. "centimeter" is semantically equivalent to "centimeters".
1. "K" (the thousands symbol in compact decimals) is NOT semantically equivalent to "K" (the measurement unit Kelvin).

The above description describes the expected output. Internally, the implementation may determine the equivalent units of measurement by passing the codes back from the number formatters, allowing for a precise determination of "semantically equivalent".

Two semantically equivalent tokens can be *collapsed* if they appear at the start of both values or the end of both values.
However, the implementation may choose different levels of aggressiveness with regard to collapsing tokens.
An API for displaying ranges should permit control over whether the tokens are collapsed or not, and the levels of aggressiveness.
The currently recommended heuristic is:

1. Never collapse scientific or compact notation. This is to avoid producing ambiguous strings such as "3–5M" (could represent 3–5,000,000 or 3,000,000–5,000,000).
2. Only collapse if the tokens are more than one code point in length. This is to increase clarity of strings such as "$3–$5".
3. To perform the collapse, remove the token that is closest to the range separator.
That is, for a prefix element, remove from the end value of the range, and for a suffix element remove it from the start value of the range:
    * USD 2 – USD 5 ⇒ USD 2 – 5
    * 2M EUR – 5M EUR ⇒  2M – 5M EUR
    * 2 km – 5 km ⇒ 2 – 5 km
    * 2M ft – 5M ft ⇒ 2M – 5M ft
4. When the tokens can have distinct plural forms, modify the remaining token so that it has the correct plural form. That is, use [Plural Ranges](#Plural_Ranges) to calculate the correct plural category for the range, and pick the variant of that the remaining token corresponding to that plural form.

In bidi contexts, the data is built so that rule #3 works **visually**.
For example, if a range from 2 km to 5 km would be presented visually as "_mk 5 – mk 2_", the collapsed form would be "_mk 5 – 2_".
(The _mk_ is a stand-in for the native representation.)
This requires consistent visually reordering among the elements: the range, the prefixes and the suffixes.
Thus a prefix value will be reordered to be visually a suffix value, and the order of the range will be visually reversed.

### <a name="Range_Pattern_Processing" href="#Range_Pattern_Processing">Range Pattern Processing</a>

To obtain a number range pattern, the following steps are taken:

1. Load the range pattern found in [Miscellaneous Patterns](#Miscellaneous_Patterns).
1. Optionally add spacing to the range pattern.

To determine whether to add spacing, the currently recommended heuristic is:

1. If the *lower* string ends with a character other than a digit, or if the *upper* string begins with a character other than a digit.
2. If the range pattern does not contain a character having the `White_Space` binary Unicode property after the `{0}` or before the `{1}` placeholders.

These heuristics may be refined in the future.

To add spacing, insert a non-breaking space (U+00A0) at the positions in item 2 above.

* * *

© 2001–2025 Unicode, Inc.
This publication is protected by copyright, and permission must be obtained from Unicode, Inc.
prior to any reproduction, modification, or other use not permitted by the [Terms of Use](https://www.unicode.org/copyright.html).
Specifically, you may make copies of this publication and may annotate and translate it solely for personal or internal business purposes and not for public distribution,
provided that any such permitted copies and modifications fully reproduce all copyright and other legal notices contained in the original.
You may not make copies of or modifications to this publication for public distribution, or incorporate it in whole or in part into any product or publication without the express written permission of Unicode.

Use of all Unicode Products, including this publication, is governed by the Unicode [Terms of Use](https://www.unicode.org/copyright.html).
The authors, contributors, and publishers have taken care in the preparation of this publication,
but make no express or implied representation or warranty of any kind and assume no responsibility or liability for errors or omissions or for consequential or incidental damages that may arise therefrom.
This publication is provided “AS-IS” without charge as a convenience to users.

Unicode and the Unicode Logo are registered trademarks of Unicode, Inc. in the United States and other countries.