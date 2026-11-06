# 云购生活新零售数仓项目 · 第 2 周

**主题**：数据清洗进阶 · Hive 四层数仓建模 · 探索性分析
**团队**：ydsh-team　**成员**：张宇（20230308059）
**前置仓库**：第 1 周（环境搭建 + ODS 接入 + 质量预评估 + 初步清洗）见 [ydsh-week1](https://github.com/yuyu41-sg/ydsh-week1)

## 本周任务

1. 枚举字段标准化（6 字段 case when 映射 + 收敛验证）
2. 异常值与格式标准化（P95 截断 / 三格式日期统一 / 手机号 / 行为时间）
3. 字段拆分合并与编码映射（order_timestamp / region / 编码维度表）
4. 数据质量对比报告（五类脏数据修复率量化）
5. Hive 四层数仓架构设计（ODS→DWD→DWS→ADS）
6. DWD 层星型建模与分区加载（4 维度表 + dwd_order 事实表按月分区 ORC）
7. 探索性数据分析（KPI / 多维分布 / 窗口函数）

## 目录结构

```
├── sql/
│   ├── clean/          # 子任务1-3：枚举标准化 / 异常值格式 / 字段合并编码映射
│   ├── dwd/            # 子任务6：DWD 星型建模 DDL + 动态分区加载
│   ├── quality/        # 子任务4：清洗前后修复率统计
│   └── analysis/       # 子任务7：探索性分析 eda.sql
├── 数据质量对比报告.docx     # 子任务4 交付物
├── 数仓架构设计文档.docx     # 子任务5 交付物
├── 探索性分析简报.docx       # 子任务7 交付物
└── 第2周_AI协同日志.md       # 每周必交（按官方模板，7 条记录）
```

## 关键成果

- 枚举全部收敛（gender 8→3、member_level 11→3、order_status 12→6、pay_type 7→4、channel 5→4、behavior_type 10→4）
- 修复率：age/email/重复/金额/数量/格式 全部 100%（launch_date 11 条遗留不填充，理由见报告）
- dwd_order 15000 行按月 12 分区（选型依据：日均仅 625 行，按天分区为小文件灾难）+ user_id 4 桶
- 核心指标：已完成订单 5642 / GMV ¥14,151,214.68 / 客单价 ¥2,508.19 / ARPU ¥14,193.80

## 运行方式

```bash
# 在 Hive 环境中按序执行（容器内路径 /sql 已挂载本目录）
beeline -u jdbc:hive2://localhost:10000 -f /sql/clean/enum_standard.sql
beeline -u jdbc:hive2://localhost:10000 -f /sql/clean/outlier_format.sql
beeline -u jdbc:hive2://localhost:10000 -f /sql/clean/field_merge.sql
beeline -u jdbc:hive2://localhost:10000 -f /sql/dwd/01_create_dwd.sql
beeline -u jdbc:hive2://localhost:10000 -f /sql/dwd/02_load_dwd.sql
beeline -u jdbc:hive2://localhost:10000 -f /sql/analysis/eda.sql
```

> 前置：第 1 周环境与 ODS 层基础表（见 ydsh-week1 仓库）。
