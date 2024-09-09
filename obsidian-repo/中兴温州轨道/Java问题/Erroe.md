
*对应整数的类型是不能使用JSON对象来进行时数据的Send
-- -
 Cannot deserialize value of type `long` from Object value (token `JsonToken.START_OBJECT`); nested exception is com.fasterxml.jackson.databind.exc.Misma
 *


##### 1 全局禁用eslint-disable-next-line @typescript-eslint/no-use-before-define Es报错问题

```TypeScript
module.exports = {
  // 你的其他配置
  rules: {
    '@typescript-eslint/no-use-before-define': 'off',
  },
};

```

*在.eslintrc.js*
```TypeScript
module.exports = {
  // 其他配置
  rules: {
    '@typescript-eslint/no-use-before-define': 'off',
  },
};

```
*在 `.eslintrc.json` 中全局禁用*

```TypeScript
{
  "rules": {
    "@typescript-eslint/no-use-before-define": "off"
  }
}

```

*在 `.eslintrc` 中全局禁用*
```TypeScript
{
  "rules": {
    "@typescript-eslint/no-use-before-define": "off"
  }
}

```

*在 `.eslintrc.yaml` 中全局禁用*
```TypeScript
rules:
  @typescript-eslint/no-use-before-define: off

```