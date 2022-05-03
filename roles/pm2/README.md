# pm2

Launch and reload Node.js applications and even static files.

## Task summary

- Install dependencies according to the node version installed on the machine
- Serve static files over http
- Restart web apps
- Delete logs by applications or all
- Deploy applications
- Restart applications
- Save applications

## Requirements

- `pm2` npm package should be installed.

  ```sh
  npm i -g pm2
  ```

- An `ecosystem.config.js` file must exist (just for Node.js apps) in the project to deploy. Please go to the official documentation if you want to read more about [Ecosystem File](https://pm2.keymetrics.io/docs/usage/application-declaration)

## Role Variables

The role uses these variables:

| Variable        | Default value           | Required | Description                                                                                |
| --------------- | ----------------------- | -------- | ------------------------------------------------------------------------------------------ |
| project_name    | my-awesome-project      | No       | Name of the project to deploy. Usefull when you want to deploy static files                |
| project_path    | -                       | Yes      | Path where the project is                                                                  |
| entry_point     | ecosystem.config.js     | No       | Entry point of the project                                                                 |
| app_names       | -                       | No       | An array of names that will be deployed. They should exist in the ecosystem.config.js file |
| serve_port      | 4040                    | No       | Port where the app is going to listenning                                                  |
| log_date_format | YYYY-MM-DDTHH:mm:ss.SSS | No       | Prefix logs with custom formated timestamp                                                 |

Variables with default values are defined in the `defaults/main.yml` file.

## Dependencies

This role requires a **node_env** variable that should be set in the playbook where the role is used. The possible values are: `production`, `demo` or `staging`.

## Example Playbook

### Serving static files

Serve your static files (like a frontend app) over HTTP. Let's say you have a project with the following structure:

```bash
# my-app
.
├── index.js
├── package.json
└── public
    └── index.html
```

So if you want to deploy your project in a remote server, just create a playbook like this:

    - hosts: webapps
      roles:
        - {
            role: pm2,
            project_path: /var/www/my-app
            project_name: my-app,
            entry_point: public/index.html
          }

That will deploy the app `my-app` on the default port `4040`.

### Deploy specific apps

You can deploy specific applications declared in an `ecosystem.config.js` file. Let' say you have a project with this structure:

```bash
# my-awesome-app
.
├── api.js
├── ui.js
├── worker.js
├── package.json
├── ecosystem.config.js
```

And the `ecosystem.config.js` file looks like this:

```js
// ecosystem.config.js
module.exports = {
  apps: [
    {
      name: 'worker',
      script: './worker.js',
    },
    {
      name: 'ui',
      script: './ui.js',
      exec_mode: 'fork',
    },
    {
      name: 'api-app',
      script: './api.js',
      instances: 4,
      exec_mode: 'cluster',
    },
  ],
};
```

But you only want to deploy `workers` and `ui` apps, is that possible? of course, you can do it. Create a playbook and pass an array of names with the apps that you want to deploy in the var `app_names`:

    - hosts: workers
      roles:
        - {
            role: pm2,
            project_path: /var/www/my-awesome-app
            app_names: ['workers', 'ui']
          }

The role will only deploy `workers` and `ui` applications on the remote workers' servers.

### Deploy all applications

Following up on the same example of above, let's say that you want to deploy all applications declared in the `ecosystem.config.js` file.

```
    - hosts: servers
      roles:
        - {
            role: pm2,
            project_path: /var/www/my-awesome-app
          }
```
