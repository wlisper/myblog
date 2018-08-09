{
:title "拓扑排序"
:layout :post
:tags ["algorithm"]
}

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org010e50b">1. 拓扑排序</a>
<ul>
<li><a href="#org805f137">1.1. 拓扑排序算法一：</a></li>
<li><a href="#org058f8f1">1.2. 拓扑排序算法二</a></li>
</ul>
</li>
</ul>
</div>
</div>

<a id="org010e50b"></a>

# 拓扑排序

有向无环图(AOV)的拓扑排序，是将图中的顶点排序成一个全序序列，使得对于图中的任意一条有向边(v, u)，满足在拓扑中v出现在u前面。

拓扑排序通常用于大工程中子工程执行顺序的确定，一个大工程通常可以拆分成多个子工程，有些子工程又必须在子工程之前执行，这是就可以使用拓扑排序，对所有子工程进行排序。


<a id="org805f137"></a>

## 拓扑排序算法一：

1.  选择一个入度为0的顶点并输出。
2.  从图中删除此定点和所有出边。
3.  重复1，2步。

循环结束后如果输出的顶点小于图中的顶点，则说明图中有回路。

common lisp实现如下：

    (defstruct vertex
      index  ;; vertex index
      in  ;; in edge
      out ;; out edge
      )
    
    (defparameter *graph* (make-array 6 :element-type 'vertex)) ;; vertex array
    (defun add-vertex (index in out)
      (setf (aref *graph* index)
            (make-vertex :index index
                         :in in
                         :out out)))
    
    (defun topologic-sort (&optional (graph *graph*))
      (dotimes (i (length graph))
        (if (null (aref graph i)) nil
            (if (null (vertex-in (aref graph i)))
                (progn (format t "~A~%" (vertex-index (aref graph i)))
                       (dolist (j (vertex-out (aref graph i)))
                         (setf (vertex-in (aref graph j))
                               (delete i (vertex-in (aref graph j)))))
                       (setf (aref graph i) nil)
                       (topologic-sort graph))))))
    
    ;; add five edges
    (add-vertex 0 '() '(1 2 3))
    (add-vertex 1 '(0 2) '())
    (add-vertex 2 '(0) '(1 4))
    (add-vertex 3 '(0 5) '(4))
    (add-vertex 4 '(2 3 5) '())
    (add-vertex 5 '() '(3 4))
    (topologic-sort)
    ;; output
    ;; 0
    ;; 2
    ;; 1
    ;; 5
    ;; 3
    ;; 4


<a id="org058f8f1"></a>

## 拓扑排序算法二

利用深度优先搜索的思想：

1.  构建一个栈。
2.  从任意顶点v开始：
3.  对v的所有临界顶点递归调用拓扑排序。
4.  将v压入栈中。
5.  还有未入栈顶点？跳到第2步。
6.  弹出栈中所有顶点。

common lisp实现如下：

    (defstruct vertex
      index
      adjacent)
    
    (defparameter *graph* nil)
    (defparameter *stack* nil)
    (defparameter *visited* nil)
    
    (defun add-vertex (index adjacent)
      (setf (aref *graph* index)
            (make-vertex :index index
                         :adjacent adjacent)))
    
    (defun make-graph (n)
      (setf *graph* (make-array n :element-type 'vertex))
      (setf *visited* (make-array n :initial-element nil))
      (setf *stack* nil))
    
    (defun topologic-sort-util (n &optional (graph *graph*))
      (setf (aref *visited* n) t)
      (dolist (i (vertex-adjacent (aref graph n)))
        (unless (aref *visited* i)
          (topologic-sort-util i graph)))
      (push n *stack*))
    
    (defun topologic-sort (&optional (graph *graph*))
      (dotimes (i (length *visited*))
        (unless (aref *visited* i)
          (topologic-sort-util i graph)))
      (dolist (e *stack*)
        (format t "~A~%" e)))
    
    (progn (make-graph 6)
           (add-vertex 0 '(1 2 3))
           (add-vertex 1 '())
           (add-vertex 2 '(1 4))
           (add-vertex 3 '(4))
           (add-vertex 4 '())
           (add-vertex 5 '(3 4)))

