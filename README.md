## Testing Components

all the testing functions used throughout this course.

### Index
* [test_cases](#test-cases)
* [run_tests](#run-tests)
* [log_reports](#log-reports)
* [gen_cases](#gen-cases) 
* [truth_table](#truth-table) 
* log-if-failing
* it shoulds


---

## test_cases

all testing components expect your unit tests to be defined in this format

```js
  const test_cases = [
      {name: '1', args: [1], expected: 1},
      {name: '2', args: [2], expected: 2}
    ];
```

(describe later the format for behavioral suites)

[TOP](#testing-components)

---

## run_tests

the basic unit-testing function, takes in a function & test cases.

you'll use this all the time, everywhere

works best when run in chromdev

```js
function run_tests(_target, _cases) {
  for (let t_case of _cases) {
    const expected = t_case.expected;
    const actual = _target(... t_case.args, false);

    let pass;
    if (typeof expected === 'object') {
      const _actual = JSON.stringify(actual);
      const _expected = JSON.stringify(expected);
      pass = _actual === _expected;
    } else {
      pass = actual === expected;
    };

    if (!pass) {
      console.log(`${t_case.name}: \n`);
      console.log(`   actual: ${typeof actual},`, actual);
      console.log(`   expected: ${typeof expected},`, expected);
    };
  };
};
```

[TOP](#testing-components)

---

## log_reports

takes in a function and it's test cases, prints out an object with the name of the case followed by the result of the function with those args.
It does not assert against expected values.

You'll use this when logging or analyzing code

```js
function log_reports(_target, _cases) {
  const report = {}
  for (let t_case of _cases) {
     report[t_case.name] = _target(...t_case.args) 
  };
  console.log(report)
};
```

[TOP](#testing-components)

---

## gen_cases

generates a comprehensive set of boolean test cases for any given function

used in 'boolean algebra' to build truth tables and practice derivations

```js
function gen_cases(f) {

  const num_args = f.length;

  // generate an array of all binary strings of size num_args
  const all_binary_strings = [];
  const max_binary = parseInt("1".repeat(num_args),2);
  for(let n = 0; n <= max_binary; n++){
    const bin_str = n.toString(2);
    const padded_str = bin_str.padStart(num_args,'0');
    all_binary_strings.push(padded_str);
  }

  // map each binary string to an array of booleans
  const arr_o_cases = str_cases_arr.map(
            (str) => { return str.split('').map(
                (x) => Boolean(Number(x))
              )})
  
  // build a test_cases object around each array of booleans
  const cases = [];
  for (let args of arr_o_cases) {
    const _case = {};
    _case.name = String(args);
    _case.args = args;
    _case.expected = f(...args);
    cases.push(_case);
  };

  return cases

}
```

[TOP](#testing-components)

---

## truth_table

takes a boolean function and comprehensive test cases, logs a truth table of it's behavior.   it does not assert

you will use this in 'boolean algebra' to study & compare the behavior of boolean expressions


```js
function truth_table(_target, _cases) {
  const report = {}
  for (let t_case of _cases) {
    report[t_case.name] = _target(...t_case.args);
  };
  console.table(report);
};
```

[TOP](#testing-components)








___
___
### <a href="http://janke-learning.org" target="_blank"><img src="https://user-images.githubusercontent.com/18554853/50098409-22575780-021c-11e9-99e1-962787adaded.png" width="40" height="40"></img> Janke Learning</a>
