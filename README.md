# security-keycloak-admin-client

- [Keycloak Admin Clientの使用](https://ja.quarkus.io/version/3.27/guides/security-keycloak-admin-client)

# Keycloakの準備

事前にKeycloakを起動しておき必要な設定を済ませておく。

QuarkusのDev Servicesによるコンテナ版Keycloakの自動起動は設定していないので別途起動しておく必要がある。

1. ポート8081で起動する

   ```
   bin/kc.sh start-dev --http-port=8081
   ```

2. レルム "quarkus" を作成する

3. クライアント "quarkus-client" を作成する

   このときコンフィデンシャルクライアントにしてサービスアカウントを有効にする。

   - Client authentication: On
   - Service accounts roles: Checked

   このクライアントを用いて接続するので生成されたクライアントシークレットを控えておく。

4. Service accounts rolesタブでrealm-managementクライアントの "realm-admin" ロールを付与する

   管理REST APIを使用するには適切な権限が必要になる。
   "realm-admin" よりも弱い必要十分な権限で済ませるのがよい。

# Dev modeで起動

```
./mvnw quarkus:dev -Dquarkus.keycloak.admin-client.client-secret=<生成されたクライアントシークレット>
```

クライアントシークレットは src/main/resources/application.properties に直接設定してもよい。

src/main/java/org/acme/keycloak/admin/client/RolesResource.java にて /api/admin/roles のパスで
レルム "quarkus" のレルムロールの一覧を返すようにコーディングしている。

```
$ curl -s http://localhost:8080/api/admin/roles | jq .
[
  {
    "id": "fe9a091b-3afc-4cc1-b392-d5ba13bba40a",
    "name": "default-roles-quarkus",
    "description": "${role_default-roles}",
    "scopeParamRequired": null,
    "composite": true,
    "composites": null,
    "clientRole": false,
    "containerId": "d0401fdd-37a8-46a9-a9ae-d76ea560c079",
    "attributes": null
  },
  {
    "id": "e92aa721-4ac9-4ff9-ae4a-c5f5a3356146",
    "name": "uma_authorization",
    "description": "${role_uma_authorization}",
    "scopeParamRequired": null,
    "composite": false,
    "composites": null,
    "clientRole": false,
    "containerId": "d0401fdd-37a8-46a9-a9ae-d76ea560c079",
    "attributes": null
  },
  {
    "id": "9772e9a9-f257-448f-8ea1-d175661226d3",
    "name": "offline_access",
    "description": "${role_offline-access}",
    "scopeParamRequired": null,
    "composite": false,
    "composites": null,
    "clientRole": false,
    "containerId": "d0401fdd-37a8-46a9-a9ae-d76ea560c079",
    "attributes": null
  }
]
```

# Links

- [OpenID Connect (OIDC)のDev ServicesとDev UI](https://ja.quarkus.io/version/3.27/guides/security-openid-connect-dev-services)
- [realm-managementのクライアントロール一覧](https://docs.redhat.com/ja/documentation/red_hat_build_of_keycloak/26.4/html/server_administration_guide/admin_permissions#realm_specific_roles)
- [Javaのkeycloak-admin-clientのJavaDoc](https://access.redhat.com/webassets/avalon/d/red_hat_build_of_keycloak-26.4/javadocs/org/keycloak/admin/client/package-summary.html)
- [Javaのkeycloak-admin-clientのテストコード](https://github.com/keycloak/keycloak-client/tree/main/testsuite/admin-client-tests)

