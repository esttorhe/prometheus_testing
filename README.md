# prometheus_testing
Simple and naive package for easier testing of prometheus metrics

## Motivation

While I was working with [`prometheus`][prometheus] I wanted to be sure that my counters were being called when I was expecting them to and also that they were being upped the correct number of times as well as with the proper labels (when it applies).

[`prometheus`][prometheus] provides certain testing helpers but none were what I was expecting; so I decided to create a simple set of helpers to easily verify counters modifications with the desired values.

## Usage

```go
// Let's import with a `.` to make the functions available in the current context
// If you don't want to do this make sure to prepend the functions with `promtest`
import (
   . "github.com/esttorhe/prometheus_testing"
)

func TestClass_MethodUpsPrometheusCounter_WhenConditionsAreMet(t *testing.T) {
   // Create if needed the counter we are interested in checking
   // otherwise just jump over to the registration part
	counterName := "Counter"
	counter := prometheus.NewCounterVec(prometheus.CounterOpts{
		Namespace: "Namespace",
		Subsystem: "Subsystem",
		Name:      "Name",
		Help:      "Help",
   }, []string{"label"})
   
   // Create a new registry just for testing
   // Let's avoid messing with our production registry
   reg := prometheus.NewRegistry()
   reg.MustRegister(counter)
   defer reg.Unregister(counter)

   sut := SubjectUnderTest{}
   sut.Method()
   // Check that the counter was upped in the registry (`reg`)
   // The «current» value after upping should be `1` (as passed in the parameter)
   // Because this is a `CounterVec` we can pass an unlimited ammount of `ExpectationLabelPair`s that
   // will be used to check that everything was upped using the proper labels.
   CheckPrometheusCounterVec(t, reg, counter, 1,
      ExpectationLabelPair{LabelName: "label", LabelValue: rule.Name},
   )
}
```

The other 2 helper functions should be used exactly like the example above.
The other 2 are:

| Function                             | Description                                                                                                                           |
| :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| `CheckPrometheusCounterVec`          | Helper method that checks that a prometheus counter vec has the expected value for the expected label pairs on the passed in registry |
| `CheckPrometheusCounterVecNotCalled` | Helper method that checks that a prometheus counter vec doesn't get called with a set of parameters on the passed in registry         |
| `CheckPrometheusCounter`             | Helper method that checks that a prometheus counter has the expected value on the passed in registry                                  |

## Author
__Esteban Torres__ 

- [![](https://img.shields.io/badge/twitter-esttorhe-brightgreen.svg)](https://twitter.com/esttorhe) 
- ✉ me+github_prometheus_testing@estebantorr.es

## License

`prometheus_testing` is available under the MIT license. See the [LICENSE](LICENSE) file for more info.

[prometheus]:https://prometheus.io