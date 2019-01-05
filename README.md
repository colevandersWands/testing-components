## Testing Components

all the testing functions used throughout this course.

### Index
* [test_cases](#test-cases)
* [run_tests](#run-tests)
* [run_tests_logging](#run-tests-logging)
* [log_reports](#log-reports)
* [gen_cases_from_exp](#gen-cases-from-exp) 
* [gen_bool_cases_from_seed](#gen-bool-cases-from-seed) 
* [truth_table](#truth-table) 
* [test_dom_component](#test-dom-component) 
* [test_closure_app](#test-closure-app)


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

## 

```js
function run_tests_logging(_target, _cases, _log) {
  for (let t_case of _cases) {
    let expected = t_case.expected;

    let actual = _target(...t_case.args, false);

    let pass;
    if (typeof expected === 'object') {
      const _actual = JSON.stringify(actual);
      const _expected = JSON.stringify(expected);
      pass = _actual === _expected;
    } else {
      pass = actual === expected;
    };

    if (!pass && _log) {
      console.log(`${t_case.name}: \n`);
      console.log(`   actual: ${typeof actual},`, _target(...t_case.args, true));
      console.log(`   expected: ${typeof expected},`, expected);
    } else if (!pass) {
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

## gen_cases_from_exp

generates a comprehensive set of boolean test cases for any given function

used in 'boolean algebra' to build truth tables and practice derivations

```js
function gen_cases_from_exp(f) {

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
  const to_boolean = (x) => Boolean(Number(x));
  const to_arr_o_bools = (str) => { return str.split('').map(to_boolean) };
  const arr_o_cases = all_binary_strings.map(to_arr_o_bools);

  
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


## gen_bool_cases_from_seed

(maybe too specific to belong here?)

generates a comprehensive set of boolean test cases with a given number of arguments.

the first arg determines the number of arguments in the test cases.
the second arg determines which set of expected values.  this allows you to talk about or repeat the same challenge without having to retype all your cases

used to define challenges in 'solving boolean solutions'

```js
function gen_bool_cases_from_seed(num_args, seed) {
  const num_tests = Math.pow(2, num_args);
  const possible_tables = Math.pow(2, num_tests);

  if (seed > possible_tables) {
    const msg = 'seed is larger than 2^(2^'+num_args+') = '+possible_tables; 
    const err = new Error(msg);
    throw err;
  };

  const str_cases_arr = [];

  // generate an array of all binary strings of size num_args
  const max_bin = parseInt("1".repeat(num_args),2);
  for(let n = 0; n <= max_bin; n++){
    const bin_str = n.toString(2);
    const padded_str = bin_str.padStart(num_args,'0');
    str_cases_arr.push(padded_str);
  }

  // convert strings to arrays of booleans
  const to_boolean = (x) => Boolean(Number(x));
  const to_arr_o_bools = (str) => { return str.split('').map(to_boolean) };
  const arr_o_cases = str_cases_arr.map(to_arr_o_bools);
  
  // reverse for alignment with nested if/elses
  arr_o_cases.reverse();

  // generate expected values from seed                                                                        
  const bin_str = seed.toString(2);                       
  const padded_str = bin_str.padStart(num_tests,'0');     
  const arr_o_strs = padded_str.split('');                
  const expecteds = arr_o_strs.map(to_boolean);   

  const cases = [];

  for (let i = 0; i < num_tests; i++) {
    const _case = {};                       
    _case.name = String(arr_o_cases[i]);    
    _case.args = arr_o_cases[i];            
    _case.expected = expecteds[i];          
    cases.push(_case);                      
  };

  return cases

}
```

[TOP](#testing-components)

---


## gen_num_cases_from_seed

(maybe too specific to belong here?)

generates from a seed comprehensive test cases with a given number of arguments, and a given number of possible number return values

the first arg determines the number of arguments in the test cases.
the second arg determines which set of expected values.  this allows you to talk about or repeat the same challenge without having to retype all your cases
the third arg determins how many distinct expected values are possible

this is used in 'multi-body conditionals' to generate exercises

```js
function gen_num_cases_from_seed(num_args, seed, base) {
  const num_tests = Math.pow(2, num_args);
  const possible_tables = Math.pow(base, num_tests);

  if (seed > possible_tables) {
    const msg = 'seed is larger than '+base+'^(2^'+num_args+') === '+possible_tables; 
    const err = new Error(msg);
    throw err;
  };

  const str_cases_arr = [];

  // generate an array of all binary strings of size num_args
  const max_bin = parseInt("1".repeat(num_args),2);
  for(let n = 0; n <= max_bin; n++){
    const bin_str = n.toString(2);
    const padded_str = bin_str.padStart(num_args,'0');
    str_cases_arr.push(padded_str);
  }

  // convert strings to arrays of booleans
  const to_boolean = (x) => Boolean(Number(x));
  const to_arr_o_bools = (str) => { return str.split('').map(to_boolean) };
  const arr_o_cases = str_cases_arr.map(to_arr_o_bools);

  // reverse for alignment with nested if/elses
  arr_o_cases.reverse();

  // generate expected values from seed                                    
  const based_str = seed.toString(base);                       
  const padded_str = based_str.padStart(num_tests,'0');     
  const expect_strs = padded_str.split('');                
  const expecteds = expect_strs.map(Number);

  const cases = [];

  for (let i = 0; i < num_tests; i++) {
    const _case = {};                       
    _case.name = String(arr_o_cases[i]);    
    _case.args = arr_o_cases[i];            
    _case.expected = expecteds[i];          
    cases.push(_case);                      
  };

  return cases

}
```

[TOP](#testing-components)

---

## truth_table

-> use this more generally for displaying test cases? <-

takes test cases 

you will use this in 'boolean algebra' to study & compare the behavior of boolean expressions


```js
function truth_table(_cases, _expression) {
  const report = {}
  if (_expression) {
    for (let t_case of _cases) {
      report[t_case.name] = _expression(...t_case.args);
    };
  } else {
    for (let t_case of _cases) {
      report[t_case.name] = t_case.expected;
    };
  }
  console.table(report);
};
```

[TOP](#testing-components)


---

## test_dom_component

used to render components into a testing div for inspecting & confirmation

```js

function test_dom_component(_target, _cases) {
  const test_div = document.getElementById('componentest');
  const component_name = document.createTextNode(_target.name);
  test_div.appendChild( document.createElement("br") );
  test_div.appendChild(component_name);
  test_div.appendChild( document.createElement("br") );

  for (let t_case of _cases) {
    try {
      const new_component = _target(...t_case.args);
      test_div.appendChild( new_component );
    } catch(err) {
      console.log(component_name, t_case.name);
      console.log(err);
    }
  }; 
}
```
[TOP](#testing-components)

---

## test_closure_app

tests the closure framework from [course frameworks](https://github.com/colevanderswands/course-frameworks)

```js

function test_closure_app(_app, _tests) {
  let log = [];
  for (let test of _tests) {
    let result = run_actions(_app, test.actions);
    log.push({[test.it_should]: result});
  }
  console.log(log)


  function run_actions(_app, _cases) {

    let log = {};

    for (let i = 0; i < _cases.length; i++) {

      let result = action_assert(_app, _cases[i]);
      if (result !== true) {
        log[i] = result;
      }

    };
    if (Object.keys(log).length === 0) {
      return true
    } else {
      return log
    }
  };

  function action_assert(_app, _test) {
    let action = _test.action;
    let args = _test.args;
    let expected = _test.expected;
    if (expected === null) {
      return true;
    }

    let actual = _app(action, ...args);

    let pass;
    if (typeof expected === 'object') {
      const _actual = JSON.stringify(actual);
      const _expected = JSON.stringify(expected);
      pass = _actual === _expected;
    } else {
      pass = actual === expected;
    };

    if (pass) {
      return true
    } else {
      return { action, actual, expected  }
    };
  };

}

```


___
___
### <a href="http://janke-learning.org" target="_blank"><img src="https://user-images.githubusercontent.com/18554853/50098409-22575780-021c-11e9-99e1-962787adaded.png" width="40" height="40"></img> Janke Learning</a>
