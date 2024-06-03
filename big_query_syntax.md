# kf-finalproject
--Pembuatan Tabel Baru (berisi gabungan seluruh tabel)
CREATE TABLE kimia_farma.kf_combined as (
--Memilih kolom yang akan ditampilkan serta pendefinisian kolom tambahan
SELECT DISTINCT transaction_id, date, kf_final_transaction.branch_id, 
branch_name, kota, provinsi, kf_kantor_cabang.rating as rating_cabang, customer_name, 
kf_final_transaction.product_id, kf_product.product_name,
--Menamakan ulang 'price' menjadi 'actual_price', sesuai panduan
kf_final_transaction.price as actual_price, discount_percentage, 
-- Mendefinisikan persentase_gross_laba
  CASE
    WHEN kf_final_transaction.price <= 50000 THEN '10%'
    WHEN kf_final_transaction.price > 50000 THEN '15%'
    WHEN kf_final_transaction.price > 100000 THEN '20%'
    WHEN kf_final_transaction.price > 300000 THEN '25%'
    WHEN kf_final_transaction.price > 500000 THEN '30%'
  END as persentase_gross_laba,
-- Mendefinisikan nett_sales
  kf_final_transaction.price*(100-discount_percentage) as nett_sales,
-- Mendefinisikan net_profit
    CASE
    WHEN kf_final_transaction.price <= 50000 THEN kf_final_transaction.price*(100-discount_percentage)*0.10
    WHEN kf_final_transaction.price > 50000 THEN kf_final_transaction.price*(100-discount_percentage)*0.15
    WHEN kf_final_transaction.price > 100000 THEN kf_final_transaction.price*(100-discount_percentage)*0.20
    WHEN kf_final_transaction.price > 300000 THEN kf_final_transaction.price*(100-discount_percentage)*0.25
    WHEN kf_final_transaction.price > 500000 THEN kf_final_transaction.price*(100-discount_percentage)*0.30
  END as nett_profit,
  --Menamakan ulang 'rating' menjadi 'rating_transaksi', sesuai panduan
kf_final_transaction.rating as rating_transaksi, 
-- Kolom dipilih dari tabel berikut
FROM kimia_farma.kf_final_transaction
-- Penggabungan tabel kf_product
FULL OUTER JOIN kimia_farma.kf_product
ON kf_product.product_id = kf_final_transaction.product_id
-- Penggabungan tabel kf_inventory
FULL OUTER JOIN kimia_farma.kf_inventory
ON kf_inventory.branch_id = kf_final_transaction.branch_id
-- Penggabungan tabel kf_kantor_cabang
FULL OUTER JOIN kimia_farma.kf_kantor_cabang
ON kf_kantor_cabang.branch_id = kf_final_transaction.branch_id
)
