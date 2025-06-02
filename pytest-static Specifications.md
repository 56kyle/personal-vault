---
tags:
  - pytest-static
  - pytest-plugin
  - repo
---
Overall Goal:
	Design a Python Pytest Plugin that can be used for getting all possible return values from a given callable based off of its type annotations.

Goal Clarifications:
- A callable is meant quite litterally. It could be anything callable so long as it has a signature with annotations

Requirements:
- Needs to work for Python >= 3.7, may make use of version specific imports as desired
- This functionality should be called from a pytest marker.
- This marker should mimic @pytest.mark.parametrize mostly, but be adapted in order to make its true output more apparent.
- Since python's fundamental types contain an infinite number of instance in many cases, please make sure to have a predefined mapping for handling these cases and just returning a few instances of said type instead of an infinite number of them.
- When receiving multiple argnames and argtypes, please make sure that it is parametrized to return all possible combinations of argvalues, where each argvalues set is all possible types that the argtype can be. So for example, if we are saying the set of all possible int's is {1, 2} and all possible str's is {'a', 'b'}, then if I parametrized with argnames=['foo', 'bar'] and argtypes=[int, Union[int, str]] then that would mean I would call the function with each of the following arg sets:
  [(1, 1), (1, 2), (1, 'a'), (1, 'b'), (2, 1), (2, 2), (2, 'a'), (2, 'b')]
- When getting a callable's possible instance values, rather than just mapping the return type's possible values, instead make sure to map all possible combinations of parameter combinations and to call the callable with each, and then to return all return values received.

Example Usages:
(Since I would like you to come up with the best name possible for these methods, I shall simply refer to the marker as "example_name")
(Additionally, to specify some more complex cases, if I say "IntParams" I am referring to the predetermined set of int values that we will use to represent all possible int values and within instantiation. Same goes for BoolParams, StrParams, ComplexParams, etc.)

@pytest.mark.example_name(argnames=['foo'], argtypes=[int])
def test_foo(foo: int):
	assert isinstance(foo, int)

@pytest.mark.example_name(argnames=['foo', 'bar'], argtypes=[int, str], ids=lambda x: str(x) + 'foo')
def test_foo(foo: int, bar: str):
	assert isinstance(foo, int)
	assert isinstance(bar, str)

def demo_func(a: int) -> str:
	return str(a) + 'suffix'
 
@pytest.mark.example_name(argnames='foo, bar', argtypes=[int, demo_func])
def test_foo(foo: int, bar: str):
	assert isinstance(foo, int)
	assert isinstance(bar, str)
	assert bar.endswith('suffix')

class DemoClass:
	a: int
	b: str

@pytest.mark.example_name(argnames='foo', argtypes=[Dict[str, Union[str, DemoClass]]])
def test_foo(foo: Dict[str, Union[str, DemoClass]]):
	# Assuming IntParams = {1, 2} and StrParams = {'a', 'b'}, then
	# this test should run with the following arg values
	# \[
	#	(foo={'a': 'a'}),
	#	(foo={'a': 'b'}),
	#	(foo={'a': DemoClass(1, 'a')}),
	#	(foo={'a': DemoClass(1, 'b')}),
	#	(foo={'a': DemoClass(2, 'a')}),
	#	(foo={'a': DemoClass(2, 'a')})
	#  (foo={'b': 'a'}),
	#	(foo={'b': 'b'}),
	#	(foo={'b': DemoClass(1, 'a')}),
	#	(foo={'b': DemoClass(1, 'b')}),
	#	(foo={'b': DemoClass(2, 'a')}),
	#	(foo={'b': DemoClass(2, 'a')})
	# \]
	assert isinstance(foo, dict)
	assert len(foo.keys()) == 1

Common Pitfalls:
- While at first glance this would seem like a great use case for sets so that you can minimize duplication, it is a bit harder to implement than would be expected due to the need to support non-hashable types. If you do choose to use sets, please make sure to take hashability into account.
- Some callables aren't exaustive in their types. For example, the type Dict[str, int] doens't truly accept any string since dict keys have a minimum length. Since our pytest plugin is ultimately a tool for automating this kind of edge case testing, we need to make sure to properly propogate errors and be able to isolate what args were passed through that led to this issue. Traceability is a must since we are creating a tool used for testing.

Style Recommendations:
- Use type hints everywhere, this code needs to be able to meet MyPy strict standards.
- Use 3.7 compatible type hints such as Union.
- Try to break functions apart where possible to make unit testing easier.

Final Thoughts:
- Please explain your reasoning for any large scale architecture design decisions
- If you have any questions that you believe would aid you in your decision making please don't hesistate to ask















