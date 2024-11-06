pg-anon
=============
# Fork of @rap2hpoutre pg-anonymizer library

Export your PostgreSQL database anonymized. Replace all sensitive data thanks to `faker`. Output to a file that you can easily import with `psql`.

## Usage

Run this command by giving a connexion string or .sql file path and an output file name (no need to install first thanks to `npx`):

```bash
  npx pg-anon --input=postgres://user:secret@localhost:1234/mydb --faker-locale=ru -o backend-dump.sql --transformer=./scripts/anon-extensions.js --columns=public.users.name:extension.firstName,public.users.second_name:extension.lastName,public.users.third_name:extension.middleName,public.users.phone:extension.phone,public.users.email:extension.email
  npx pg-anon --input=./path-to.sql --faker-locale=ru -o backend-dump.sql --transformer=./scripts/anon-extensions.js --columns=public.users.name:extension.firstName,public.users.second_name:extension.lastName,public.users.third_name:extension.middleName,public.users.phone:extension.phone,public.users.email:extension.email
```

☝️ This command requires `pg_dump`. It may already be installed as soon as PostgreSQL is installed.

## API

### `--input`

Use `--input` option to specify path to .sql file or to specify the connection to psql database.

### `--columns | -c`

You can choose which faker function you want to use to replace data (default is `faker.random.word`):

```bash
npx pg-anonymizer postgres://localhost/mydb \
  --columns=firstName:faker.name.firstName,lastName:faker.name.lastName
```

### `--extension`

#### Use an extension file to create your own custom replacements

Create an extension file, written in javascript
```javascript
// myExtension.js
module.exports = {
  maskEmail: (email) => {
   const [name, domain] = email.split('@');
   const { length: len } = name;
   const maskedName = name[0] + '...' + name[len - 1];
   const maskedEmail = maskedName + '@' + domain;
   return maskedEmail;
  }
};
```

Pass the path to `--extension` and use the module exports in `--columns`
```bash
npx pg-anonymizer postgres://localhost/mydb \
  --extension=./myExtension.js \
  --columns=email:extension.maskEmail
```

### `--skip`

#### Skip tables

Use `--skip` to skip anonymizing entire tables

### `--preserve-null | -n`

#### Preserve `NULL` values

Use `--preserve-null` to skip anonymization on fields with `NULL` values.

### `--faker-locale`

#### Set fakers locale (i18n)

Use `--faker-locale` to change the locale used by faker (default: `en`)

### Also supports pg_dump args when works with connection

```bash
  npx pg-anon [--lib-args] -- [pg_args]
```

### Example

```bash
  npx pg-anon --input=./path-to.sql --faker-locale=ru -o backend-dump.sql --transformer=./scripts/anon-extensions.js --columns=public.users.name:extension.firstName,public.users.second_name:extension.lastName,public.users.third_name:extension.middleName,public.users.phone:extension.phone,public.users.email:extension.email
```
