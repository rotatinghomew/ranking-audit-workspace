# 业务资料协议

全部交易与关系均为合成防御性审核资料。企业集中采购是正常业务类型，配送点共用不证明异常。family是同一次采购的多份记录线索，只有duplicate_of或有适用证据的裁决才确认重复。audit_records的概率只适用于random抽核，不可将定向裁决视为随机。proof_required里的物流或明确重复证据需要与付款事实共同审理。

所有数字、身份和业务来件均为合成。记录的稳定编号只在对应表内唯一，跨表关联以明确字段为准。布尔值为原来源明确回报，空字符串引用表示没有已知对应。金额如有均用整数分，比例为0至1，其他单位在字段中注明。

## merchants

商家经营类型，不预设可疑结论。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| category | string | 食品类目 |
| channel | string | 经营渠道 |

## transactions

合成交易和多渠道登记。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| family | string | 同一次采购的登记家族 |
| merchant_id | string | 商家 |
| buyer | string | 用途内匿名购买者 |
| buyer_kind | string | enterprise或household |
| delivery_group | string | 经授权可用的共同配送点键 |
| ordered_at | string | 实际下单时点 |
| known_at | string | 记录获知时间 |
| units | number | 登记数量 |
| amount_minor | number | 实际金额分 |
| payment_status | string | paid或cancelled |
| shipment_proof | string | documented为有凭据，pending为待取得 |
| duplicate_of | string | 已确认重复的原登记；空串未确认 |

引用关系：merchant_id 对应 merchants.id。

## refunds

后续退款，不删除原交易。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| transaction_id | string | 原交易 |
| units | number | 实际退货数量 |
| known_at | string | 收到退货资料时间 |
| paid_at | string | 真实退款时点 |

引用关系：transaction_id 对应 transactions.id。

## audit_records

人工审核历史原件，选中方式保留。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| transaction_id | string | 审查交易 |
| selection | string | random为概率抽核，targeted为疑点，appeal为主动申诉 |
| probability | nullable_number | 概率抽核实际选中概率 |
| verdict | string | eligible、ineligible或pending |
| scope | string | 裁决仅限本交易或明示重复登记 |
| known_at | string | 裁决可知时间 |
| evidence_id | string | 本次实际核对的物流/付款证据键 |

引用关系：transaction_id 对应 transactions.id。

## appeals

商家后续提出的新证据来件。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| transaction_id | string | 申诉对应交易 |
| submitted_at | string | 实际提交时间 |
| known_at | string | 资料收到时间 |
| new_document | string | 新证明的可审读中文摘要 |
| allowed_reviewers | array | 有权查看的审核人 |

引用关系：transaction_id 对应 transactions.id。

## reviewers

审核资源与证据权限。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| evidence_rights | array | 可审资料类型 |
| minutes_per_day | number | 每天可用分钟 |
| active_case | nullable_string | 仍在受理的交易 |
| accepted_until | string | 已承诺工作期限 |

## procurement_adoptions

采购方基于旧名单发出的邀请和确认。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| merchant_id | string | 被邀请商家 |
| invitation_at | string | 邀请时间 |
| accepted_at | nullable_string | 实际接受时间 |
| ranking_cutoff | string | 当时名单的事实截止 |
| contract_valid_to | string | 已接受采购约定的结束日 |

引用关系：merchant_id 对应 merchants.id。

## policies

榜单公开采用的资格与稳定性政策。

| 字段 | 格式 | 含义 |
| --- | --- | --- |
| id | string | 记录的稳定编号 |
| refund_window_days | number | 成熟退款观察天数 |
| minimum_probability_support | number | 总体误计量估计的最小随机支持 |
| max_rank_uncertainty | number | 采购可采用的名次跨度上限 |
| proof_required | array | 正式否决需要的证据类型 |
