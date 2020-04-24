# TestCafeTest
This repo has a docker image for nodejs and chome,firefox browsers

### Configurations for githubactions
```yaml
name: TestCafeCI

on: [push]

jobs:
  build:
    name: Run testcafe tests
    runs-on: ubuntu-latest
    container:
      image: dipjyotimetia/testcafetest
    strategy:
      matrix:
        node: [12.x]
    steps:
      - uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}
      - uses: actions/checkout@v1
      - run: npm ci
      - run: npm test
        env:
          CI: true

```
### Use the below configuration in runner

```js
const createTestCafe = require('testcafe');

const setupTestCafe = async () => {
    const testCafe = await createTestCafe('localhost', 1337, 1338);
    try {
        const runner = testCafe.createRunner();
        await runner
            .src('tests/*.test.ts')
            .browsers('chrome:headless --no-sandbox --disable-gpu')
            .reporter(['spec',{
                name: 'xunit',
                output: 'reports/report.xml'
            }, {
                name: 'html',
                output: 'reports/reports.html'
            }])
            .screenshots({
                takeOnFails: true,
                fullPage: true,
                path: "screenshot",
                pathPattern: "${DATE}_${TIME}/test-${TEST_INDEX}/${USERAGENT}/${FILE_INDEX}.png"
            })
            .run({
                skipJsErrors: true,
                quarantineMode: true,
                selectorTimeout: 50000,
                assertionTimeout: 7000,
                pageLoadTimeout: 8000,
                stopOnFirstFail: true,
                tsConfigPath: "tsconfig.json",
            });
        console.log('Test finished');    
        testCafe.close();
    } catch (error) {
        console.log(error);
        testCafe.close();
    }
}

setupTestCafe();
```
