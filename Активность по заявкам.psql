with filter as (SELECT 
		t.id
	FROM public.ticket t
		left join queue q on q.id = t.queue_id
	where q.name like '%IT%' 
	group by t.id)
, history as (
SELECT 
	th.ticket_id,
	CASE
		WHEN ts.name in ('closed successful','pending auto close+','pending auto close-','pending reminder','closed unsuccessful','closed with workaround') THEN 'closed'
		WHEN ts.name in ('waiting on third party supplier') THEN '3-rd line'
		WHEN ts.name in ('waiting on change') THEN 'in progress'

		ELSE ts.name
	END as state,
	q.name as queue,
	tht.name as ht_name,
	th.name as history_name, 
	date_trunc('day', th.create_time) as create_time
FROM public.ticket_history th
	right join filter f on f.id = th.ticket_id
	left join ticket_state ts on th.state_id = ts.id
	left join queue q on q.id = th.queue_id
	left join ticket_history_type tht on tht.id = th.history_type_id
where q.name like '%IT%' and tht.name in ('StateUpdate','Move')
order by th.ticket_id,th.create_time)

select 
	count(h.ticket_id) as value,
	h.state as metric,
	h.create_time as time
from history h
group by h.state, h.create_time
order by  h.create_time,h.state