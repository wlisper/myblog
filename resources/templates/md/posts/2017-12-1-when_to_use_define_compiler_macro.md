{
:title "何时使用compiler macro"
:layout :post
:tags ["lisp"]
}

define-compiler-macro用于定义编译器宏。

按照CLHS的说法：unlike an ordinary macro, a compiler macro can decline to provide an expansion merely by returning a form that is the same as the original(which can be obtaned by using &whole)。

    (defun square(x) (expt x 2))
    (define-compiler-macro square (&whole form arg)
        (if (atom arg)
            `(expt ,arg 2)
            (case (car arg)
                (square (if (= (length arg) 2)
                            `(expt ,(nth 1 arg) 4)
                            form))
                (expt (if (= (length arg) 3)
                          (if (numberp (nth 2 arg))
                              `(expt ,(nth 1 arg) ,(* 2 (nth 2 arg)))
                              `(expt ,(nth 1 arg) (* 2 ,(nth 2 arg))))
                          form))
                (otherwise `(expt ,arg 2)))))
    
    (funcall (compiler-macro-function 'square) '(square x) nil) ;;  => (EXPT X 2)
    (funcall (compiler-macro-function 'square) '(square (square x)) nil) ;; => (EXPT X 4)
    (funcall (compiler-macro-function 'square) '(funcall #'square x) nil)  ;; => (EXPT X 2)

编译器宏通常用于极度优化特定的代码片段。如果一个算法在参数为常量是可以非常快，那么编译器宏在此种情形下会返回一个极快的版本；如果参数不为常量，编译器宏返回通常的算法实现。
编译器宏与普通宏的区别在于编译器宏可以遮蔽(shadow)一个同名函数，这使你可以在没有可以优化的可能时调用同名的函数实现。
通常情况下，你不需要使用编译器宏，除非你想要极度优化你的代码, 编译器宏牺牲编译时间换取更快的运行速度。

例子：

    (define-compiler-macro fib (n &whole w)
        (if (intergerp n)
            ;; one case
            w))

编译时，编译器无法知道(fib x) 中x的类型，因此，不会被优化；但是在(fib 10)中，编译器知道10的类型满足integerp，因此(fib 10)的值会被计算插入到原来的代码处。

