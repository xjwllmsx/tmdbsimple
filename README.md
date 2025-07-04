tmdbsimple
==========

![Python package](https://github.com/celiao/tmdbsimple/workflows/build/badge.svg)
![codecov](https://img.shields.io/codecov/c/github/celiao/tmdbsimple)
![pypi](https://img.shields.io/pypi/pyversions/tmdbsimple)
![pypi](https://img.shields.io/pypi/v/tmdbsimple)

A wrapper for The Movie Database API v3
---------------------------------------
*tmdbsimple* is a wrapper, written in Python, for The Movie Database (TMDb) API
v3.  By calling the functions available in *tmdbsimple* you can simplify your
code and easily access a vast amount of movie, tv, and cast data.  To learn
more about The Movie Database API, check out the [overview](
http://www.themoviedb.org/documentation/api) and [documentation](
https://developers.themoviedb.org/3).

Features
--------
- COMPLETELY UPDATED AND FULLY TESTED.
- Supports only [Python versions with TLS
  1.2](http://pyfound.blogspot.com/2017/01/time-to-upgrade-your-python-tls-v12.html).
  Keep it simple!
- Tested with Python 3.9, 3.10, and 3.11.
- One-to-one mapping between *tmdbsimple* methods and TMDb endpoints.
- Implements all TMDb methods, including Accounts and Authentication.
- Easy to access data using Python class attributes.
- Easy to experiment with *tmdbsimple* functions inside the Python interpreter.
- Code tested with unittests.  Refer to the unittest code for method call syntax.

Installation
------------
*tmdbsimple* is available on the Python Package Index (PyPI) at
https://pypi.python.org/pypi/tmdbsimple.

You can install *tmdbsimple* using one of the following techniques.

- Use pip:

```
pip install tmdbsimple
```

- Download the .zip or .tar.gz file from PyPI and install it yourself
- Download the [source from Github](http://github.com/celiao/tmdbsimple) and
  install it yourself

If you install it yourself, also install
[requests](https://requests.readthedocs.io/en/master/).

API Key
-------
You will need an API key to The Movie Database to access the API.  To obtain a
key, follow these steps:

1) Register for and verify an [account](https://www.themoviedb.org/account/signup).
2) [Log into](https://www.themoviedb.org/login) your account.
3) Select the API section on left side of your account page.
4) Click on the link to generate a new API key and follow the instructions.

Examples
--------
Once you have the *tmdbsimple* package installed and a TMDb API key, you can
start to play with the data.

First, import the library and assign your API_KEY.

```python
import tmdbsimple as tmdb
tmdb.API_KEY = 'YOUR_API_KEY_HERE'
```

_Optionally_, set a timeout for requests. See [here](https://docs.python-requests.org/en/master/user/advanced/#timeouts) for more info.

```python
tmdb.REQUESTS_TIMEOUT = 5  # seconds, for both connect and read
```

or

```python
tmdb.REQUESTS_TIMEOUT = (2, 5)  # seconds, for connect and read specifically 
```

_Optionally_, configure the library to use your own REQUESTS_SESSION. See [here](https://docs.python-requests.org/en/master/user/advanced/#session-objects) for more info.

```python
import requests
tmdb.REQUESTS_SESSION = requests.Session()
```

To communicate with The Movie Database API, create an instance of one of the
object types, call one of the methods on the instance, and access the instance
attributes.  Use keys to access the values of attributes that are dictionaries.
In this example, we create a movie instance for 'The Matrix' and determine the
budget and certification.

```python
    >>> movie = tmdb.Movies(603)
    >>> response = movie.info()
    >>> movie.title
    'The Matrix'
    >>> movie.budget
    63000000
    >>> response = movie.releases()
    >>> for c in movie.countries:
    ...    if c['iso_3166_1'] == 'US':
    ...         print(c['certification'])
    ...
    'R'
```

Let's play with the interface a bit more.  Suppose you and your friend are
arguing over which movie in the Bourne series was most popular.  Your friend
says the first in a series is always most popular.  You disagree.

```python
    >>> search = tmdb.Search()
    >>> response = search.movie(query='The Bourne')
    >>> for s in search.results:
    ...     print(s['title'], s['id'], s['release_date'], s['popularity'])
    ...
    The Bourne Ultimatum 2503 2007-08-03 55.2447062124256
    The Bourne Supremacy 2502 2004-07-23 43.4553609681985
    The Bourne Identity 2501 2002-06-06 38.5531563780592
    The Bourne Legacy 49040 2012-08-10 9.90635210153143
    The Bourne Identity 8677 1988-05-08 1.53988446573129
    Bette Bourne: It Goes with the Shoes 179304  0.23
```

You are correct!  Now you claim the producers should be able to make sequels
cheaper, based on what they learned from making the first movie.  To be fair,
you compute the budget per minute of runtime.  Your friend disagrees, claiming
the producers spend more money trying to out do the previous sequel.

```python
    >>> identity = tmdb.Movies(2501)
    >>> response = identity.info()
    >>> identity.budget, identity.runtime
    (60000000, 119)
    >>> int(identity.budget/identity.runtime)
    504201
    >>> supremacy = tmdb.Movies(2502)
    >>> response = supremacy.info()
    >>> supremacy.budget, supremacy.runtime
    (75000000, 108)
    >>> int(supremacy.budget/supremacy.runtime)
    694444
    >>> ultimatum = tmdb.Movies(2503)
    >>> response = ultimatum.info()
    >>> ultimatum.budget, ultimatum.runtime
    (70000000, 115)
    >>> int(ultimatum.budget/ultimatum.runtime)
    608695
```

In this case you are both correct.  The third movie was cheaper than the
second, which was more expensive than the first.

You also can call one of the methods without explicitly instanciating an
object.

```python
    >>> response = tmdb.Movies(603).info()
    >>> response['budget']
    63000000
```

If you use Authentication to access a user Account, be sure to check out
https://developer.themoviedb.org/docs/authentication-user.

If you like this wrapper, and would like access to even more movie and TV data,
check out *rtsimple* https://pypi.python.org/pypi/rtsimple, a wrapper for the
Rotten Tomatoes API.
