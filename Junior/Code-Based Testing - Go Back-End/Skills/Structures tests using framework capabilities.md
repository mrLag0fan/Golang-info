## Uses t_.Fatal, t.Error_ during test execution to indicate a failure.
## Uses _t.Skip_ to skip a test execution when needed.
## Uses _t.Run_ to create and execute subtests.
## Creates _TestMain_ for some extra test setup (log, cfg, etc).
## Uses t._TempDir_ when a directory is needed for the test.
## Verifies the test results using standart constructions or some test framewoks like _assert.Equal_ from [stretchr/testify](https://github.com/stretchr/testify).