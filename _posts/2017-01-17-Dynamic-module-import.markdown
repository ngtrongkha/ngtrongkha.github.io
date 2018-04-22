---
layout: post
title:  "Dynamic module loading"
date:   2017-07-17 21:17:00
description: Some python tricks.
tags: [coding, python]
---

Thông thường chúng ta import class bằng một khai báo từng thành phần từ module đến class. Ví dụ:

```py
import os
```
Trong ví dụ trên thì os là một module được gán tĩnh, khi chúng ta muốn import một module khác thì phải sửa mã nguồn.
Tuy nhiên trong trường hợp chúng ta muốn import tự động hơn thì chúng ta sử dụng cách sau.
Đoạn code tham khảo từ project django-oscar.

```py
import traceback


def _import_module(module_label, classnames):
    """
    Imports the module with the given name.
    Returns None if the module doesn't exist, but propagates any import errors.
    """
    try:
        return __import__(module_label, fromlist=classnames)
    except ImportError:
        # There are 2 reasons why there could be an ImportError:
        #
        #  1. Module does not exist. In that case, we ignore the import and
        #     return None
        #  2. Module exists but another ImportError occurred when trying to
        #     import the module. In that case, it is important to propagate the
        #     error.
        #
        # ImportError does not provide easy way to distinguish those two cases.
        # Fortunately, the traceback of the ImportError starts at __import__
        # statement. If the traceback has more than one frame, it means that
        # application was found and ImportError originates within the local app
        __, __, exc_traceback = sys.exc_info()
        frames = traceback.extract_tb(exc_traceback)
        if len(frames) > 1:
            raise
```
