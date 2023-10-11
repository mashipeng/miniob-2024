## obclient
略

## observer
### common
 - global_context：一些如pool manager的全局对象
 - init_setting：一些配置参数
 - init：服务器初始化
 - types：一些type设置
 - rc：函数返回码
### event
 - session_event：SQL语句和SQL执行信息，处理SQL请求
 - sql_debug：SQL语句执行时的调试信息
 - sql_event：SQL语句解析后每个阶段的事件，明显一个SQL请求对应session，分为多个sql_event
 - storage_event：没什么好讲的
### net
 - buffered_writer：写入socket的缓存，socket是用来客户端和服务器通信的
 - cli_communicator：使用命令行进行通讯，标准输入输出
 - communicator：与客户端进行通讯（文本/mysql协议）
 - mysql_communicator：mysql协议通讯
 - plain_communicator：文本协议通讯
 - ring_buffer：环型缓存（内存中）
 - server_param：服务端通讯启动参数
 - server：与客户端连接，接受请求，创建communicator监听
### session
 - session_stage：会话中event分为各个阶段（stage）
 - session：会话，处理请求
 - thread_data：session中thread的数据
### sql
#### executor
 - command_executor：直接使用executor执行，如DDL
 - create_index_executor：略
 - create_table_executor：略
 - desc_table_executor：描述表的executor
 - help_executor：help语句executor
 - execute_stage：执行SQL语句的Stage，包括DML和DDL，根据前面阶段生成的结果，有些语句会生成执行计划，有些不会。整体上分为两类，带执行计划的，或者 CommandExecutor 可以直接执行的。
 - load_data_executor：导入数据executor
 - set_variable_executor：set语句executor
 - show_tables_executor：略
 - sql_result：SQL执行结果，如果当前SQL生成了执行计划，那么在返回客户端时，调用执行计划返回结果。否则返回的结果就是当前SQL的执行结果
 - trx_begin_executor：事务开始语句的执行器
 - trx_end_executor：事务结束的执行器，可以是提交或回滚
#### expr
 - expression：SQL内部一些表达式，如ADD，Join等
 - tuple_cell：用于tuple文件操作
 - tuple：把元组当成数据一行，对其进行投影等操作
#### operator
 - calc_logical_operator：计算逻辑算子
 - calc_logical_operator：计算物理算子
 - delete_logical_operator：delete逻辑算子
 - delete_physical_operator：delete物理算子
 - explain_logical_operator：略
 - explain_physical_operator：略
 - index_scan_physical_operator：索引搜索物理算子
 - insert_logical_operator：略
 - insert_physical_operator：略
 - join_logical_operator：略
 - join_physical_operator：略
 - logical_operator：逻辑算子类型
 - physical_operator：物理算子类型
 - predicate_logical_operator：谓词/过滤逻辑算子
 - predicate_physical_operator：过滤/谓词物理算子
 - project_logical_operator：略
 - project_physical_operator：略
 - string_list_physical_operator：字符串列表物理算子
 - table_get_logical_operator：表示从表中获取数据的算子（比如使用全表扫描、通过索引获取数据等）
 - table_scan_physical_operator：表扫描物理算子
#### optimizer
 - comparison_simplification_rule：简单比较的重写规则，如果有简单的比较运算，比如比较的两边都是常量，那我们就可以在运行执行计划之前就知道结果，进而直接将表达式改成结果，这样就可以减少运行时的计算量。
 - conjunction_simplification_rule：简化多个表达式联结的运算，比如只有一个表达式，或者表达式可以直接出来
 - expression_rewriter：重写子表达式
 - logical_plan_generator：略
 - optimize_stage：将解析后的Statement转换成执行计划，并进行优化，分为多阶段，实际上没有optimize
 - physical_plan_generator：根据逻辑计划生成物理计划。不做任何优化
 - predicate_pushdown_rewriter：将一些谓词表达式下推到表数据扫描中，这样可以提前过滤一些数据
 - predicate_rewrite：谓词重写规则，有些谓词可以在真正运行之前就知道结果，那么就可以提前运算出来，比如1=1,1=0。
 - rewrite_rule：逻辑计划的重写规则
 - rewriter：根据规则对逻辑计划进行重写
#### parser
 - lex_sql：不懂
 - yacc_sql：不懂
 - parse_defs：表示各个语句，如update
 - parse_stage：解析SQL语句，解析后的结果可以参考parse_defs.h
 - parse：单纯调用已经写的函数解析
 - resolve_stage：执行Resolve，将解析后的SQL语句，转换成各种Stmt(Statement), 同时会做错误检查
 - value：类型属性与各个方法
#### plan_cache
 - plan_cache_stage：尝试从Plan的缓存中获取Plan，如果没有命中，则执行Optimizer，实际上没实现，可以尝试
#### query_cache
 - query_cache_stage：查询缓存处理，当前什么都没做
#### stmt
 - calc_stmt：描述算术运算语句
 - create_index_stmt：略
 - create_table_stmt：略
 - delete_stmt：略
 - desc_table_stmt：描述表
 - exit_stmt：断开连接
 - explain_stmt：略
 - filter_stmt：过滤语句
 - help_stmt：略
 - insert_stmt：略
 - load_data_stmt：略
 - select_stmt：略
 - set_variable_stmt：略
 - show_tables_stmt：略
 - stmt：SQL语句解析后通过Resolver转换成Stmt，Statement的类型
 - trx_begin_stmt：事务begin，未被实现
 - trx_end_stmt：事务end，未被实现
 - update_stmt：略

### storage
#### buffer
 - disk_buffer_pool：BufferPool 的实现
 - frame：页帧是磁盘文件在内存中的表示。磁盘文件按照页面来操作，操作之前先映射到内存中，将磁盘数据读取到内存中，也就是页帧。
 - page：表示一个页面，可能放在内存或磁盘上
#### clog
 - clog：CLog 就是 commit log。或者等价于redo log。
#### common
 - condition_filter：内存中过滤？
 - meta_util：meta文件的函数
#### db
 - db：一个DB对应一个目录，所有的表和数据都放置在这个目录下。启动时，从指定的目录下加载所有的表和元数据。
#### default
 - 与db有关的存储操作
#### field
 - field_meta：字段元数据
 - field：字段
#### index
 - bplus_tree_index：B+树索引，调用bplus_tree
 - bplus_tree：B+树的实现
 - index_meta：描述一个索引，一个索引包含了表的哪些字段，索引的名称等。
 - index：索引基类，后继可能实现其他索引类型
#### persist
 - persist：持久化存储，写入硬盘
#### record
 - record_manager：负责管理在一个文件/页面上表记录(行)的组织/管理
 - record：标识一个记录的位置，单纯对一个记录
#### table
 - table_meta：表元数据
 - table：表，存储记录
#### trx
 - latch_memo：内存锁
 - mvcc_trx：多版本并发事务
 - trx：事务相关
 - vacuous_trx：没有实现事务功能，可能作为无事务时的参考