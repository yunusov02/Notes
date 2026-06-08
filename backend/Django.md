
In Django, environment-based settings means using different configurations for different environments

| **Environment** | **Purpose**                                                |
| --------------- | ---------------------------------------------------------- |
| Development     | Local machine where you write code                         |
| Staging         | A server that mimics production for testing before release |
| Production      | Real server used by customers                              |

to do that we need to delete `settings.py` and create directory called settings and there create files
`
* `__init__.py`
* `base.py`
* `development.py`
* `staging.py
* `production.py`


delete `SECRET_KEY` `DEBUG` `DATABASES` from base.py

