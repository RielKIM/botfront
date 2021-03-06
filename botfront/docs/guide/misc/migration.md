# Migration guide

As we haven't reached 1.x we consider minor versions as major and they could have breaking changes.

## Minor versions
For example  `0.18.1` -> `0.18.3`
Botfront will offer you to upgrade your project. Part of the process is automated, buty you will still have **ONE thing** to do manually:
Take:
- Take `rasa-for-botfront` image version in `.botfront/botfront.yaml`. In the following it would be `v1.5.1-bf.10` (with the `v`)
- Take `rasa-sdk` image version in `.botfront/botfront.yaml`. In the following it would be `1.3.2` (without the `v`)

```yaml{4,5}
images:
  default:
    ...
    rasa: botfront/rasa-for-botfront:v1.5.1-bf.10
    actions: rasa/rasa-sdk:1.3.2
    ...
```

And apply these versions to:
- `rasa/Dockerfile`
- `actions/Dockerfile`
- `actions/Dockerfile.production`

## Major versions

### 0.18.x -> 0.19.x

#### Breaking changes

We significantly changed (and improved) how bot responses work. Only three types remain:
- Text
- Text with buttons
- Image
- Custom

If you were using other response types (mostly facebook related such as Templates, Lists, ...) they will be lost. **You need to make a copy before upgrading.**

#### Upgrading a project
::: warning The project structure has changed.
You will need to create another project and copy your data in it:
:::

1. Create a new project with `botfront init`.
2. Copy the `botfront-db` folder from your old project to the newly created project. Make sure to copy and not move your db so you can always recover it from your existing project. Your existing project should remain unchanged.
3. If you have custom actions, copy them to the `actions` folder in the new project.
4. Run your project with `botfront up`. At this point, you should be able to log in.
5. Go to `Settings > Endpoints`
If you are running with the default CLI configuration, replace:

```yaml
nlg:
  url: 'http://botfront-api:8080/project/bf/nlg'
```

with:
```yaml
nlg:
  type: 'rasa_addons.core.nlg.GraphQLNaturalLanguageGenerator'
  url: 'http://botfront:3000/graphql' # This should be the same host as the Botfront app
```

6. Restart Rasa with `botfront restart rasa`
7. Train
8. You're done, have fun :)

