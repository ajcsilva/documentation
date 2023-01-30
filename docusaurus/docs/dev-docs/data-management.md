---
title: Data Management System 
description: Import, export, and transfer data using the Strapi CLI
displayed_sidebar: devDocsSidebar
canonicalUrl: https://docs.strapi.io/developer-docs/latest/development/export-import.html
---

# Data Management System

:::callout 🚧 Feature under development
The data management system is under development. Not all use cases are covered by the initial release. You can provide feedback about desired functionality on the [Strapi feedback website](https://feedback.strapi.io). The feature is available in v4.6.0 and later versions.

:::

Occasionally you need to move data out of or into a Strapi instance. The data management system allows you to efficiently extract data from an existing instance or archive and import that data into a separate instance. Strapi provides CLI-based commands that allow you to export and import data. Common use cases include:

- creating a data backup,
- restoring data from a backup.

The following documentation details examples of how to use the `strapi export` and `strapi import` commands.

:::strapi Using the Command Line Interface (CLI)
The `strapi export` and `strapi import` CLI commands with all of the available options are listed in the [Command Line Interface documentation](/dev-docs/cli#strapi-export).
:::

## Export data using the CLI tool

The `strapi export` command by default exports data as an encrypted and compressed `tar.gz.enc` file. The default export command exports:

- the project configuration,
- entities: all of your content,
- links: relations between your entities,
- assets: files stored in the uploads folder,
- schemas,
- the `metadata.json` file.

:::caution
Admin users and API tokens are not exported.
:::

### Name the export file

Exported data are contained in a `.tar` file that is automatically named using the format `export_YYYYMMDDHHMMSS`. You can optionally name the exported file by passing the `--file` or `-f` option with the `strapi export` command. Do not include a file extension.

#### Example: Export data with a custom filename
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --file my-strapi-export
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --file my-strapi-export
```

</TabItem>

</Tabs>

### Configure data encryption

The default `strapi export` command encrypts your project data using `aes-128-ecb` encryption and adds the file extension `.enc`. To use encryption you need to pass an encryption key using the `-k` or `--key` option or enter an encryption key when prompted. The encryption key is a `string` with no minimum character count.

:::tip Encryption keys
Strong encryption keys are encouraged to protect sensitive data in your project. [OpenSSL](https://www.openssl.org/) is a resource for generating encryption keys. The following example commands generate encryption keys in a terminal:

<Tabs>

<TabItem value="mac" label="Mac/Linux">

```bash
openssl rand -base64 48
```

</TabItem>

<TabItem value="windows" label="Windows">

```bash
node -p "require('crypto').randomBytes(48).toString('base64');"
```

</TabItem>

</Tabs>

:::

To disable encryption, pass the `--no-encrypt` option with the `strapi export` command.

#### Example: Export data without encryption

<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --no-encrypt
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --no-encrypt
```

</TabItem>

</Tabs>

#### Example: Export data with the encryption `--key` option

<br/>
<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --key my-encryption-key
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --key my-encryption-key
```

</TabItem>

</Tabs>

### Disable data compression

The default `strapi export` command compresses your project data using `gzip` compression and adds the `.gz` file extension.

To disable compression, pass the `--no-compress` option with the `strapi export` command.

#### Example: Export data without compression
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --no-compress
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --no-compress
```

</TabItem>

</Tabs>

### Export only selected types of data

The default `strapi export` command exports your content (entities and relations), files (assets), project configuration, and schemas. The `--only` option allows you to export only the listed items by passing a comma-separated string  with no spaces between the types. The available values are `content`, `files`, and `config`. Schemas are always exported, as schema matching is used for `strapi import`.

:::note
Media such as images consist of the file (asset) and the entity in the database. If you use the `--only` flag to export `content`, the asset database records are still included, and could render as broken links.
:::

#### Example: Export only entities and relations
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --only content
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --only content
```

</TabItem>

</Tabs>

### Exclude items from export

The default `strapi export` command exports your content (entities and relations), files (assets), project configuration, and schemas. The `--exclude` option allows you to exclude content, files, and the project configuration by passing these items in a comma-separated string with no spaces between the types. You can't exclude the schemas, as schema matching is used for `strapi import`.

:::note
Media such as images consist of the file (asset) and the entity in the database. If you use the `--exclude` flag to remove assets, the database records are still included, and could render as broken links.
:::

#### Example: Export data excluding assets, entities, and relations
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi export --exclude files,content
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi export -- --exclude files,content
```

</TabItem>

</Tabs>

## Import data using the CLI tool

:::warning

- `strapi import` deletes all existing data, including the database and uploads directory, before importing the backup file.
- The source and target schemas must match to successfully use `strapi import`, meaning all content types must be identical.
- Restored data does not include the `Admin users` table, which means that `createdBy` and `updatedBy` are empty in a restored instance.  

:::
### Specify the import file

To import data into a Strapi instance use the `strapi import` command in the project root directory. Specify the file to be imported using the `-f` or `--file` option. The filename, extension, and path are required. If the file is encrypted, you are prompted for the encryption key before the import starts.

#### Example: Minimum command to import data from a file in the Strapi project root
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi import -f export_20221213105643.tar.gz.enc
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi import -- -f export_20221213105643.tar.gz.enc
```

</TabItem>

</Tabs>

### Provide an encryption key

If you are importing data from an encrypted file the encryption key can be passed with the `strapi import` command by using the `-k` or `--key` option.

#### Example: Pass the encryption key with the `strapi import` command
<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi import -f export_20221213105643.tar.gz.enc --key my-encryption-key
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi import -- -f export_20221213105643.tar.gz.enc --key my-encryption-key
```

</TabItem>

</Tabs>

### Bypass all command line prompts

When using the `strapi import` command, you are required to confirm that the import will delete the existing database contents. The `--force` flag allows you to bypass this prompt. This option is particularly useful for implementing `strapi import` programmatically. For programmatic use, you must also pass the `--key` option for encrypted files.

#### Example of the `--force` option

<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi import -f export_20221213105643.tar.gz.enc --force --key my-encryption-key
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm run strapi import -- -f export_20221213105643.tar.gz.enc --force --key my-encryption-key
```

</TabItem>

</Tabs>

### Exclude data types during import

The default `strapi import` command imports your content (entities and relations), files (assets), project configuration, and schemas. The `--exclude` option allows you to exclude content, files, and the project configuration by passing these items in a comma-separated string with no spaces between the types. You can't exclude the schemas, as schema matching is used for `strapi import`.

:::warning
Any types excluded from the import will be deleted in your target instance. For example, if you exclude `config` the project configuration in your target instance will be deleted.
:::

:::note
Media such as images consist of the file (asset) and the entity in the database. If you use the `--exclude` flag to remove assets, the database records are still included, and could render as broken links.
:::

#### Example: exclude assets from an import

<br/>

<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi import -f export_20221213105643.tar.gz.enc --exclude files
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm strapi import -- -f export_20221213105643.tar.gz.enc --exclude files
```

</TabItem>

</Tabs>

### Include only specified data types during import

The default `strapi import` command imports your content (entities and relations), files (assets), project configuration, and schemas. The `--only` option allows you to export only the listed items by passing a comma-separated string  with no spaces between the types. The available values are `content`, `files`, and `config`. Schemas are always imported, as schema matching is used for `strapi import`.

:::note
Media such as images consist of the file (asset) and the entity in the database. If you use the `--only` flag to import `content` the asset database records are still included, and could render as broken links.
:::

#### Example: import only the project configuration

<br/>
<Tabs groupId="yarn-npm">

<TabItem value="yarn" label="yarn">

```bash
yarn strapi import -f export_20221213105643.tar.gz.enc --only config
```

</TabItem>

<TabItem value="npm" label="npm">

```bash
npm strapi import -- -f export_20221213105643.tar.gz.enc --only config
```

</TabItem>

</Tabs>