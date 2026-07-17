(venv) root@zahsay:~/projects/ecommerce-platform/backend# python -m alembic upgrade head
Traceback (most recent call last):
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 144, in __init__
    self._dbapi_connection = engine.raw_connection()
                             ^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 3319, in raw_connection
    return self.pool.connect()
           ^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 448, in connect
    return _ConnectionFairy._checkout(self)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 1272, in _checkout
    fairy = _ConnectionRecord.checkout(pool)
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 712, in checkout
    rec = pool._do_get()
          ^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/impl.py", line 307, in _do_get
    return self._create_connection()
           ^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 389, in _create_connection
    return _ConnectionRecord(self)
           ^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 674, in __init__
    self.__connect()
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 900, in __connect
    with util.safe_reraise():
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/util/langhelpers.py", line 122, in __exit__
    raise exc_value.with_traceback(exc_tb)
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 896, in __connect
    self.dbapi_connection = connection = pool._invoke_creator(self)
                                         ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/create.py", line 667, in connect
    return dialect.connect(*cargs_tup, **cparams)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/default.py", line 630, in connect
    return self.loaded_dbapi.connect(*cargs, **cparams)  # type: ignore[no-any-return]  # NOQA: E501
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/psycopg2/__init__.py", line 122, in connect
    conn = _connect(dsn, connection_factory=connection_factory, **kwasync)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
psycopg2.OperationalError: could not translate host name "postgres" to address: Temporary failure in name resolution


The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/__main__.py", line 4, in <module>
    main(prog="alembic")
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/config.py", line 1047, in main
    CommandLine(prog=prog).main(argv=argv)
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/config.py", line 1037, in main
    self.run_cmd(cfg, options)
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/config.py", line 971, in run_cmd
    fn(
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/command.py", line 483, in upgrade
    script.run_env()
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/script/base.py", line 545, in run_env
    util.load_python_file(self.dir, "env.py")
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/util/pyfiles.py", line 116, in load_python_file
    module = load_module_py(module_id, path)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/alembic/util/pyfiles.py", line 136, in load_module_py
    spec.loader.exec_module(module)  # type: ignore
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "<frozen importlib._bootstrap_external>", line 995, in exec_module
  File "<frozen importlib._bootstrap>", line 488, in _call_with_frames_removed
  File "/root/projects/ecommerce-platform/backend/alembic/env.py", line 81, in <module>
    run_migrations_online()
  File "/root/projects/ecommerce-platform/backend/alembic/env.py", line 71, in run_migrations_online
    with connectable.connect() as connection:
         ^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 3295, in connect
    return self._connection_cls(self)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 146, in __init__
    Connection._handle_dbapi_exception_noconnection(
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 2450, in _handle_dbapi_exception_noconnection
    raise sqlalchemy_exception.with_traceback(exc_info[2]) from e
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 144, in __init__
    self._dbapi_connection = engine.raw_connection()
                             ^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/base.py", line 3319, in raw_connection
    return self.pool.connect()
           ^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 448, in connect
    return _ConnectionFairy._checkout(self)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 1272, in _checkout
    fairy = _ConnectionRecord.checkout(pool)
            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 712, in checkout
    rec = pool._do_get()
          ^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/impl.py", line 307, in _do_get
    return self._create_connection()
           ^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 389, in _create_connection
    return _ConnectionRecord(self)
           ^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 674, in __init__
    self.__connect()
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 900, in __connect
    with util.safe_reraise():
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/util/langhelpers.py", line 122, in __exit__
    raise exc_value.with_traceback(exc_tb)
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/pool/base.py", line 896, in __connect
    self.dbapi_connection = connection = pool._invoke_creator(self)
                                         ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/create.py", line 667, in connect
    return dialect.connect(*cargs_tup, **cparams)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/sqlalchemy/engine/default.py", line 630, in connect
    return self.loaded_dbapi.connect(*cargs, **cparams)  # type: ignore[no-any-return]  # NOQA: E501
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/projects/ecommerce-platform/backend/venv/lib/python3.12/site-packages/psycopg2/__init__.py", line 122, in connect
    conn = _connect(dsn, connection_factory=connection_factory, **kwasync)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
sqlalchemy.exc.OperationalError: (psycopg2.OperationalError) could not translate host name "postgres" to address: Temporary failure in name resolution

(Background on this error at: https://sqlalche.me/e/20/e3q8)
