SELECT regions.region_name as Region,
  percentile_cont(0.50) within group (ORDER BY end_date::date - start_date::date) as Median,
  percentile_cont(0.80) within group (ORDER BY end_date::date - start_date::date) as "80th Percentile",
  percentile_cont(0.95) within group (ORDER BY end_date::date - start_date::date) as "95th Percentile"
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON regions.region_id = customer_nodes.region_id
WHERE end_date < DATE '9999-01-01'
GROUP BY regions.region_name;
