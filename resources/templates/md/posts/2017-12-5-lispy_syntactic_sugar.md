{
:title "lispy语法糖"
:layout :post
:tags ["language"]
}

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org258c079">1. lispy语法糖</a>
<ul>
<li><a href="#org747c3bc">1.1. c风格for循环</a></li>
<li><a href="#orgc6362d0">1.2. c风格while循环</a></li>
<li><a href="#org87c8956">1.3. php风格foreach</a></li>
<li><a href="#org6bb79b5">1.4. php数组语法糖[]</a></li>
<li><a href="#orgfe963ea">1.5. 字面hash</a></li>
<li><a href="#orgab2fd74">1.6. @型注释</a></li>
</ul>
</li>
</ul>
</div>
</div>

<a id="org258c079"></a>

# lispy语法糖

语法糖虽甜，吃多了可会长蛀牙，里面还有很多bug哦。


<a id="org747c3bc"></a>

## c风格for循环

lisp实现如下：

    (defmacro for ((init cond step) &body body)
      (rutils:with-gensyms (goto-tag exit-tag)
        `(let* ,init
           (tagbody
              ,goto-tag
               (labels ((my-continue ()
                          ,step (go ,goto-tag))
                        (my-break () (go ,exit-tag)))
                 (loop
                    (unless ,cond (return))
                    ,@body
                    ,step))
              ,exit-tag))))
    
    ;; test
    (for (((i 0)) (< i 10) (incf i))
        (if (= i 6) (my-break))
        (if (evenp i) (my-continue))
        (format t "~A~%" i))
    ;; output
    ;1
    ;3
    ;5


<a id="orgc6362d0"></a>

## c风格while循环

lisp实现如下：

    (defmacro while (test &body body)
      `(for (() ,test nil)
         ,@body))


<a id="org87c8956"></a>

## php风格foreach

    (defmacro foreach ((var seq) &body body)
      (rutils:with-gensyms (seq-sym)
        `(let ((,seq-sym ,seq))
           (if (listp ,seq-sym)
               (loop for ,var in ,seq-sym
                  do (progn ,@body))
               (loop for ,var across ,seq-sym
                    do (progn ,@body))))))
    
    ;; 作用于数组
    (foreach (var #(1 2 3 4))
      (format t "~A~%" var))
    ;; 作用于链表
    (foreach (var '(1 2 3 4))
      (format t "~A~%" var))


<a id="org6bb79b5"></a>

## php数组语法糖[]

php可以这样构造数组：[1, 2, 3]。

lisp实现如下：

    (set-macro-character #\] (get-macro-character #\)))
    (set-macro-character #\[ (lambda (stream char)
                               (declare (ignore char))
                               (let* ((lst (read-delimited-list #\] stream))
                                      (res (make-array (length lst))))
                                 (dotimes (i (length lst))
                                   (setf (aref res i) (elt lst i)))
                                 res)))
    
    ;; 测试
    ;  输入:[1 2 3]   返回数组：#(1 2 3) 
    ;  输入:["hello" 3 [1 2 3]]  返回嵌套数组:#("hello" 3 #(1 2 3))。


<a id="orgfe963ea"></a>

## 字面hash

很多语言都支持字面hash表,语法如下:
{"key1" : value1, "key2" : value2};

lisp实现如下:

    (set-macro-character #\} (get-macro-character #\)))
    (set-macro-character #\{ (lambda (stream char)
                               (declare (ignore char))
                               (let* ((res (make-hash-table))
                                      (lst (read-delimited-list #\} stream)))
                                 (dolist (pair lst)
                                   (setf (gethash (car pair) res)
                                         (cdr pair)))
                                 res)))
    
    ;; 测试
    ; 输入{(1 . 2) (3 . 4) (5 . 6)}
    ; 返回哈希表


<a id="orgab2fd74"></a>

## @型注释

例如：注册一个web控制器。
@controller
public static void index() {
    //
}

声明函数为内联。
@inline
int fact(int n);

lisp实现如下：

    (set-macro-character #\@ (lambda (stream char)
                               (declare (ignore char))
                               (let ((sym (read stream))
                                     (forms (read stream)))
                                 (cond ((eq sym 'controller)
                                        (progn 
                                           (format t "register controller: ~A~%" (second forms))
                                           ;; you can do more controller register work here
                                            forms))
                                       ((eq sym 'inline)
                                        (progn (format t "declaring inline: ~A~%" (second forms)))
                                        `(,(first forms) ,(second forms) ,(third forms)
                                           (declare (inline ,(second forms)))
                                           ,@(cdddr forms)))))))
    
    ;;; test
    ;; 控制器注释
    @controller
    (defun index ()
      (format t "this is my home page~%"))
    
    ;; 内联注释
    @inline
    (defun square (n)
      (* n n))

