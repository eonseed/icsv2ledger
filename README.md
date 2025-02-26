# icsv2ledger - Enhanced CSV to Ledger Converter

[![Eonseed](https://img.shields.io/badge/Org-Eonseed-blue)](https://github.com/eonseed)

**icsv2ledger** is a powerful, interactive command-line utility that converts CSV transaction files (like those from your bank) into the [Ledger](http://ledger-cli.org) plain-text accounting format. This project is a fork of the original `icsv2ledger`, significantly enhanced with **fuzzy matching capabilities** and other improvements, now maintained under the **eonseed** organization.

## Key Features

*   **Interactive Conversion:** Prompts you for account and payee information for each transaction, learning from your choices.
*   **Fuzzy Matching:** Uses the `thefuzz` library (a modern fork of `fuzzywuzzy`) to intelligently match transaction descriptions to accounts and payees, even with typos or variations in wording. This greatly reduces the need for manual input.
*   **Learning System:** Remembers your account/payee selections in a mapping file, improving its suggestions over time.
*   **Auto-Completion:** Provides auto-completion (using the `Tab` key) for account and payee names, making input faster and more accurate.
*   **Configurable:** Supports a wide range of options via command-line arguments and a configuration file, allowing you to customize the conversion process to your specific needs.
*   **Duplicate Detection:** Can detect and optionally skip duplicate transactions, preventing accidental double-entry.
*   **Tag Support:** Allows you to add tags to transactions for more granular categorization.
*   **Template-Based Output:** Uses a customizable template to format the generated Ledger entries.
*   **Integration with Ledger:** Reads existing accounts and payees from your Ledger file to provide better suggestions.
*   **Incremental Processing:** Can process transactions incrementally, allowing you to stop and resume the conversion process.
*   **Transfer Support**: Can automatically generate double-entry transfers between accounts (even to different ledger files).

## Enhancements (Forked Version)

This version of `icsv2ledger`, maintained by **eonseed**, includes the following enhancements over the original:

*   **Fuzzy Matching:** The most significant improvement is the addition of fuzzy string matching using `thefuzz`. This makes the tool much more forgiving of variations in transaction descriptions.
*   **Fuzzy Threshold Option:** A `--fuzzy-threshold` option allows you to control the sensitivity of the fuzzy matching algorithm.
*   **Improved Robustness:** The code includes better error handling and input validation.
*   **Modern Dependencies:** Updated to use `thefuzz`, a well-maintained fork of `fuzzywuzzy`.
*   **Clearer README:** This updated README provides more detailed information and instructions.

## Synopsis

```bash
icsv2ledger.py [options] -a STR [infile [outfile]]
```

## Arguments Summary

```
infile                input filename or stdin in CSV syntax
outfile               output filename or stdout in Ledger syntax
```

## Options Summary

Options can be used from the command line or in a configuration file. `--account` is mandatory on the command line. `--config-file`, `--src-account`, and `--help` are only usable from the command line.

| Option                       | Description                                                                                                                      |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `--account STR, -a STR`       | Ledger account used as the source (mandatory on the command line).                                                               |
| `--src-account STR`           | Ledger account used as the source, *overrides* `--account`.  Command-line only.                                                   |
| `--clear-screen, -C`          | Clears the screen before each transaction prompt.                                                                                |
| `--cleared-character {*,!, }` | Character to mark a transaction as cleared.  Default: `*`.                                                                       |
| `--config-file FILE, -c FILE` | Configuration file.                                                                                                            |
| `--credit INT`                | CSV column number for credit amounts.  Default: `4`.                                                                           |
| `--csv-date-format STR`      | Date format in the CSV input file (e.g., `%Y-%m-%d`).                                                                             |
| `--csv-decimal-comma`        | Use a comma as the decimal separator in the CSV.                                                                               |
| `--currency STR`              | Currency of amounts.  Default: locale currency symbol.                                                                           |
| `--date INT`                  | CSV column number for the transaction date.  Default: `1`.                                                                      |
| `--debit INT`                 | CSV column number for debit amounts.  Default: `3`.                                                                           |
| `--default-expense STR`      | Default ledger account for destination (usually an expense).  Default: `Expenses:Unknown`.                                      |
| `--delimiter STR`            | CSV delimiter character.  Default: `,`.  Use `\t` for tab.                                                                       |
| `--desc STR`                  | CSV column number(s) for the transaction description.  Default: `2`. Can be a comma-separated list (e.g., `2,5`).              |
| `--effective-date INT`        | CSV column number for the effective date.  Requires a template file.                                                           |
| `--encoding STR`              | Text encoding of the CSV input file.  Default: `utf-8`.                                                                         |
| `--incremental`               | Appends output as transactions are processed.                                                                                   |
| `--ledger-binary FILE`        | Path to the `ledger` binary.  Usually auto-detected.                                                                            |
| `--ledger-date-format STR`    | Date format for Ledger output (e.g., `%Y/%m/%d`).  Requires `--csv-date-format`.                                                   |
| `--ledger-decimal-comma`     | Use a comma as the decimal separator in the Ledger output.                                                                      |
| `--ledger-file FILE, -l FILE` | Ledger file to read existing accounts and payees from.                                                                           |
| `--mapping-file FILE`         | File to store mappings between descriptions and payee/account/tags.                                                              |
| `--accounts-file FILE`        | File containing a list of allowed account names (for Ledger's `--strict` mode).                                                 |
| `--quiet, -q`                 | Do not prompt if the account can be deduced from the mapping.                                                                   |
| `--reverse`                   | Reverse the order of entries from the CSV file.                                                                                  |
| `--skip-dupes`                | Detect and skip duplicate transactions (based on MD5 sum).                                                                       |
| `--confirm-dupes`             | Detect duplicates and prompt the user to skip or keep them.                                                                    |
| `--skip-lines INT`            | Number of lines to skip from the beginning of the CSV file.  Default: `1`.                                                     |
| `--skip-older-than DAYS`      | Skip entries older than the specified number of days.  `-1` processes all entries.                                                |
| `--tags, -t`                  | Prompt for transaction tags.                                                                                                    |
| `--template-file FILE`        | File containing the template for Ledger output.                                                                                  |
| `--prompt-add-mappings`       | Prompt before adding new entries to the mapping file.                                                                            |
| `--entry-review`              | Display the formatted Ledger entry and prompt for confirmation (Commit, Modify, Skip).                                       |
| `--fuzzy-threshold INT`       | Minimum score (0-100) for a fuzzy match to be considered.  Default: `80`. **(New in forked version)**                            |
| `-h, --help`                  | Show this help message and exit.                                                                                                 |

## Detailed Options

Options can either be used from the command line or in a configuration file. From the command line, the syntax is `--long-option VALUE` with dashes, and in the configuration file, the syntax is `long_option=VALUE` with underscores.

There is an order of *precedence* for options. First, hard-coded defaults (documented below) are used, overridden by options from the configuration file if any, and finally overridden by options from the command line if any.

**`--account STR, -a STR`**

is the ledger account used as the source for ledger transactions. This is the only mandatory option on the command line. Default is `Assets:Bank:Current`.

When used from the command line, it is both the section name in the configuration file and the account name. The account name could then be overridden in the configuration file. See the section [Configuration file example](#configuration-file-example) where `SAV` from the command line is overridden with `account=Assets:Bank:Savings Account`.

**`--src-account STR`**

Similar to the `--account` option, it is the ledger account used as the source for ledger transactions but allows the `--account` option to be overridden *after* the config file has been parsed. This is a command-line-only option and must not be provided in any section of the config file. Use of this option allows users to treat sections of the config file as generic import recipes that can be used to import all files that use the same layout while providing a means to specify the ledger source account to use during the importing of transactions.

**`--clear-screen, -C`**

will clear the screen before every prompting. Default is `False`.

**`--cleared-character {*,!, }`**

is the character to mark a transaction as cleared. Ledger's possible values are `*` or `!` or ` `. Default is `*`.

**`--config-file FILE, -c FILE`**

is the configuration filename.

The file used will be the first found in that order:

1.  Filename given on the command line with `--config-file`,
2.  `.icsv2ledgerrc` in the current directory,
3.  `.icsv2ledgerrc` in the home directory.

**`--credit INT`**

is the CSV file column which contains _credit_ amounts. The first column in the CSV file is numbered 1. Default is `4`.

See also the documentation of the `--debit` option for negating amounts.

**`--csv-date-format STR`**

describes the date format in the CSV file.

See the [python documentation](http://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior) for the various format codes supported in this expression.

**`--csv-decimal-comma`**

will assume that numbers use the comma ',' as the decimal in the CSV.

If the `--ledger-decimal-comma` option is not set, the comma will be converted into a dot.

**`--currency STR`**

is the currency of amounts. Default is the locale currency_symbol.

**`--date INT`**

is the CSV file column which contains the transaction date. Default is `1`.

**`--debit INT`**

is the CSV file column which contains _debit_ amounts. Default is `3`.

If your bank writes all amounts in the same column, credits as positive amounts and debits as negative amounts, then set `credit` to the correct column and `debit` to `0`.

If your bank writes debits as a negative number and you want to negate the amount, then use `--debit=-3`. It will negate amounts in column 3 and use them as debit amounts.

**`--default-expense STR`**

is the default ledger account used as the destination (generally an expense) for ledger transactions. Default is `Expenses:Unknown`.

**`--delimiter STR`**

is the CSV delimiter character. Default is `,`. Special characters can be expressed using standard escape sequences, such as `\t` for a tab.

**`--desc STR`**

is the CSV file column which contains the transaction description as supplied by the bank. Default is `2`.

This _description_ will be used as the input for determining which payee and account to use by auto-completion.

It is possible to provide a comma-separated list of CSV column indices (like `desc=2,5`) that will concatenate fields in order to form a unique description. That enriched description will serve as the basis for the mapping.

**`--effective-date INT`**

is the CSV column number which contains the date to be used as the effective date. Default is `0`. Use of this option currently requires a template file. See the section [Transaction template file](#transaction-template-file).

**`--encoding STR`**

is the text encoding of the CSV input file. Default is `utf-8`. The encoding should be specified if the CSV file contains non-ASCII characters (typically in the transaction description) in an encoding other than UTF-8.

**`--incremental`**

appends output as transactions are processed. The default flow is to process all CSV input and then output the result. When `--incremental` is specified, output is written after every transaction. This allows one to stop (ctrl-c) and restart to progressively process a CSV file (`--skip-dupes` is a useful companion option).

**`--ledger-binary FILE`**

is the path to the ledger binary. Not necessary if it is in `PATH` or is at either `/usr/bin/ledger` or `/usr/local/bin/ledger`.

**`--ledger-date-format STR`**

describes the date format to be used when creating ledger entries. If `--ledger-date-format` is defined, then `--csv-date-format` must also be defined to be able to convert dates. If `--ledger-date-format` is not defined, then the date from the CSV file is reused.

See the [python documentation](http://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior) for the various format codes supported in this expression.

**`--ledger-decimal-comma`**

will assume that numbers should be printed using the comma ',' as decimal when creating ledger entries.

If the `--csv-decimal-comma` option is not set, the dot will be converted into a comma.

**`--ledger-file FILE, -l FILE`**

is the ledger filename where to get the list of already defined accounts and payees.

The file used will be the first found in that order:

1.  Filename given on the command line with `--ledger-file`,
2.  `.ledger` in the current directory,
3.  `.ledger` in the home directory.

**`--mapping-file FILE`**

is the file which holds the mapping between the description and the payee/account names to use. See the section [Mapping file](#mapping-file).

The file used will be the first found in that order:

1.  Filename given on the command line with `--mapping-file`,
2.  `.icsv2ledgerrc-mapping` in the current directory,
3.  `.icsv2ledgerrc-mapping` in the home directory.

Warning: the file must exist so that mappings are added to the file.

**`--accounts-file FILE`**

is an optional file that can be used to hold a master list of all account names, and will be used as a source for account names. See the section [Accounts file](#accounts-file).

The file used will be the first found in that order:

1.  Filename given on the command line with `--accounts-file`,
2.  `.icsv2ledgerrc-accounts` in the current directory,
3.  `.icsv2ledgerrc-accounts` in the home directory.

**`--quiet, -q`**

will not prompt if the account can be deduced from the existing mapping. Default is `False`.

**`--reverse`**

will print ledger entries in reverse of their order in the CSV file.

**`--skip-dupes`**

will attempt to detect duplicate transactions in the ledger file by comparing the MD5Sum of transactions. The MD5Sum is calculated from the formatted CSV values, including the source account. The source account is included to avoid false positives on generic transaction descriptions when the source account is different and thus should not be considered a duplicate. The MD5Sum of existing transactions are stored as a `; MD5Sum: ...` comment in the current ledger file (which means your output template will need this comment). This can help if you download statements without using a precise date range. A useful pattern is to include MD5Sum comments for both "sides" of a transaction if you download from multiple sources that resolve to a single transaction (e.g. paying a credit card from checking). Note: use of this flag by itself will detect and skip duplicate entries automatically with no interaction from the user. If you want to be prompted and determine whether to skip or not, see `--confirm-dupes`.

**`--confirm-dupes`**

Same as `--skip-dupes` but will prompt the user to indicate if they want the detected duplicate entry to be skipped or treated as a valid entry. This is useful when importing transactions that commonly contain generic descriptions.

**`--skip-lines INT`**

is the number of lines to skip from the beginning of the CSV file. Default is `1`.

**`--tags, -t`**

will interactively prompt for transaction tags. Default is `False`.

The normal behavior is for one description to prompt for payee and account, and store this in the mapping file. By setting this option, the description can also be mapped to additional tags.

At the prompt: fill in a tagname and press the Enter key as many times as you need tags. Remove an existing tag by preceding it with a minus, like `-tagname`. When finished, press the Enter key on an empty line.

This `--tags` option only *prompts* for tags. You have to add `; {tags}` in your template to make tags appear in generated Ledger transactions.

**`--template-file FILE`**

is the template filename, which contains the template to use when generating ledger transactions. See the section [Transaction template file](#transaction-template-file).

The file used will be the first found in that order:

1.  Filename given on the command line with `--template-file`,
2.  `.icsv2ledgerrc-template` in the current directory,
3.  `.icsv2ledgerrc-template` in the home directory.

**`--skip-older-than DAYS`**

will not process any entries in the CSV file which are more than DAYS old. If DAYS is negative, then the entire CSV file is processed.

**`--prompt-add-mappings`**

will prompt the user before adding entries to the mapping file. This is useful when you would prefer to manually adjust an existing entry or add the entry manually to the mapping file.

**`--entry-review`**

allows the ability to review the generated ledger entry and Commit, Modify or Skip the entry. If the entry is not committed, then the values for payee, account, and optionally tags are prompted for again.

**`--fuzzy-threshold INT`**

Sets the minimum score (0-100) for a fuzzy match to be considered a valid match.  A higher threshold means a stricter match is required.  Default is `80`.

## Configuration File Example

```ini
[SAV]
account=Assets:Bank:Savings Account
currency=AUD
date=1
csv_date_format=%d-%b-%y
ledger_date_format=%Y/%m/%d
desc=6
credit=2
debit=-1
mapping_file=mappings.SAV
fuzzy_threshold=85  ; Higher threshold for more precise matching

[SAV_addons]
beneficiary=3
purpose=4

[CHQ]
account=Assets:Bank:Cheque Account
currency=AUD
date=1
csv_date_format=%d/%m/%Y
ledger_date_format=%Y/%m/%d
desc=2
credit=3
debit=4
mapping_file=mappings.CHQ
skip_lines=0
fuzzy_threshold=75  ; Lower threshold for more flexible matching
```

## Addons

In the section [Configuration file example](#configuration-file-example), the `SAV_addons` section enables saving a CSV field value to a tag value. Those tags can then be used, for the SAV account, in your own transaction template:

```
 ; purpose: {addon_purpose}
 ; beneficiary: {addon_beneficiary}
```

## Mapping File

A typical mapping file might look like this:

```csv
/SAFEWAY/,Safeway,Expenses:Food
/ITUNES.*/,iTunes,Expenses:Entertainment
THE WRESTLERS INN,"The ""Wrestlers"" Inn",Expenses:Food
/MACY'S/,"Macy's, Inc.",Expenses:Food
MY COMPANY 1234,My Company,Income:Salary
MY COMPANY 1234,My Company 1234,Income:Salary:Tips
MY TRANSFER 1,Transfer to Savings,Transfers:Savings,transfer_to=Assets:Savings
Starbux,Starbucks,Expenses:Coffee ; Fuzzy match will help with this!
```

It uses simple string-matching by default, but if you put a '/' at the start and end of a string, it will instead be interpreted as a regular expression.

Mapping is based on your historical decisions. Later matching entries overwrite earlier ones; that is, in the example above, `MY COMPANY 1234` will be mapped to `My Company 1234` and `Income:Salary:Tips`.

**Experimental** You can use `transfer_to=` to specify another asset to make the transfer to record in a "transfer" double-entry pattern. In the example above, for the Transfers:Savings account with `transfer_to=Assets:Savings`, would create the following entries:

```
2012/01/01 Transfer to Savings
 Transfers:Savings  $100
 Assets:Checking

2012/01/01 Transfer to Savings
 Assets:Savings  $100
 Transfers:Savings
```

You can additionally add a `file=` value after `transfer_to=` to write the second entry in another file. This is useful if you split your accounts per file and want to write the first transaction in the checking file and the second in the savings file.

## Accounts File

To prevent inconsistencies, it is possible to use ledger `--strict` mode, along with a file that defines a list of allowable accounts. (See the ledger 3 manual, section 4.6 'Keeping it Consistent')

The accounts file should look like:

```
account Expenses:Food
account Expenses:Entertainment
account Income:Salary
account Income:Salary:Tips
```

All other lines will be ignored, so if you have a single ledger file that has account definitions mixed throughout it, it is safe (although potentially time-consuming) to pass it to `icsv2ledger` as the accounts-file.

## Transaction Template File

The built-in default template is as follows:

```
{date} {cleared_character} {payee}
    ; MD5Sum: {md5sum}
    ; CSV: {csv}
    {debit_account:<60}    {debit_currency} {debit}
    {credit_account:<60}    {credit_currency} {credit}
    {tags}
```

Details on how to format the template are found in the [Format Specification Mini-Language](http://docs.python.org/3/library/string.html#formatspec).

The values that can be used are: `date`, `effective_date`, `cleared_character`, `payee`, `transaction_index`, `debit_account`, `debit_currency`, `debit`, `credit_account`, `credit_currency`, `credit`, `tags`, `md5sum`, `csv`. And also the addon tags like `addon_xxxx`. See the section [Addons](#addons).

## Installation

1.  **Install `thefuzz`:**

    ```bash
    pip install thefuzz
    ```

2.  **Clone the repository:**
    ```bash
    git clone https://github.com/eonseed/icsv2ledger.git
    cd icsv2ledger
    ```
3. **Run directly or create a symbolic link**:

    ```bash
      ./icsv2ledger.py # Run directly
      ln -s /path/to/icsv2ledger/icsv2ledger.py /usr/local/bin/icsv2ledger # create symlink
    ```

## Runtime Requirements

`icsv2ledger` requires Python 3.2 or later and the `thefuzz` library. The `ledger` binary must also be installed and in your `PATH`.

## Contributing

Contributions are welcome! Please submit pull requests or open issues on the [GitHub repository](https://github.com/eonseed/icsv2ledger).

## Known Issues

On Mac OS X when CSV is passed via stdin to `icsv2ledger` you may not see any prompts offering defaults and asking for your input. This is due to an inferior readline library (libedit) installed by default on Mac OS X. Install a proper readline library and you're good to go.

```bash
sudo easy_install readline
```

On Windows the default Python installation does not provide a readline library. The [pyreadline](https://pypi.python.org/pypi/pyreadline) library provides native python emulation of this functionality and must be installed to run this utility.

## Author

`icsv2ledger` was originally created by [Quentin Stafford-Fraser](http://qandr.org/quentin). This enhanced version is maintained by the [eonseed](https://github.com/eonseed) organization, with contributions from [Vikrant Rathore](https://github.com/vikrantrathore). It also includes contributions from many others.

## See Also

*   [Ledger](http://ledger-cli.org)
*   [hledger](http://hledger.org/)
*   [thefuzz](https://github.com/seatgeek/thefuzz)
