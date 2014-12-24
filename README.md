MVC-Razor-View-de-Kodlama.
==========================
Bazan herşey Controller ‘da hazırlanamıyor. Böyle durumlarda bizzat View içerisinde kodlama gerekebiliyor. İşte bu makalede size View ‘de nasıl kodlama yaparız, bunu göstereceğim.
Bu konsepti açıklamak için Veritabanında aşağıdaki gibi bir tablo yapısı olduğunu düşünün. (Bu tabloyu mümkün mertebe basit oluşturuyorum ki mantığı anlayın.)

Tablo Adı :     yemek

Alan Adı        Alan yapısı

yemek_adı   =>    nvarchar(MAX)

opsiyon1    =>    nvarchar(MAX)

opsiyon2    =>    nvarchar(MAX)

opsiyon3     =>   nvarchar(MAX)

opsiyon1_fiyat  => money

opsiyon2_fiyat => money

opsiyon3_fiyat  => money


Amacımız Razor View ‘de  Listeleme yaparken aşağıdaki gibi bir görüntü almak.

Yemek Adı :  Yemek 1 adı

Opsiyonlar : DROPDOWN (Burada opsiyon1, opsiyon2, opsiyon3 ‘ü ve fiyatlarını birleştirip DropDown ‘da göstereceğiz.)

.....

Yemek Adı :  Yemek2 adı

Opsiyonlar : DROPDOWN (Burada opsiyon1, opsiyon2, opsiyon3 ‘ü ve fiyatlarını birleştirip DropDown ‘da göstereceğiz.)

.....

Yemek Adı :  Yemek3 adı

Opsiyonlar : DROPDOWN (Burada opsiyon1, opsiyon2, opsiyon3 ‘ü ve fiyatlarını birleştirip DropDown ‘da göstereceğiz.)

.....


MODEL

public partial class yemeklerViewModel

{

        public string yemek_adı { get; set; }
        public string opsiyon1 { get; set; }
        public string opsiyon2 { get; set; }
        public string opsiyon3 { get; set; }
        public Nullable<decimal> opsiyon1_fiyat { get; set; }
        public Nullable<decimal> opsiyon1_fiyat { get; set; }
        public Nullable<decimal> opsiyon1_fiyat { get; set; }
        
}

CONTROLLER

public ActionResult Yemekler()
{        
Var query = (from yemeks in db.yemek
select new yemeklerViewModel
{ 
yemek_adı = yemeks.yemek_Adı,
opsiyon1 = yemeks.opsiyon1,
opsiyon2 = yemeks.opsiyon2,
opsiyon3 = yemeks.opsiyon3
opsiyon1_fiyat = yemeks.opsiyon1_fiyat
opsiyon2_fiyat = yemeks.opsiyon2_fiyat
opsiyon3_fiyat = yemeks.opsiyon3_fiyat
});

Return View(query.ToList());
}



VIEW
=====
Aşağıda  Razor View 'de kodlama yapıyoruz DropDown 'ları oluşturmak için.
=====
@model IEnumerable<ProjeNameSpace.Models.yemeklerViewModel >

@foreach (var item in Model)
{
   using (Html.BeginForm("SepeteEkle", "Home", FormMethod.Post))
    {
  if (item.opsiyon1 != null || item.opsiyon2 != null || item.opsiyon3 != null)
        {
            List<SelectListItem> listItems = new List<SelectListItem>();
            if (item.opsiyon1 != null)
            {
                string fiyat = "";

                if (item.opsiyon1_fiyat != null)
                {
                    fiyat = item.opsiyon1_fiyat.ToString();
                    fiyat = fiyat.Substring(0, fiyat.Length - 2) + " TL";
                }
                listItems.Add(new SelectListItem
                {
                         Text = item.opsiyon1 + " " + fiyat,
                         Value = item.opsiyon1 + "%" + item.opsiyon1_fiyat.ToString()
                     });
            }
   if (item.opsiyon2 != null)
            {
                string fiyat = "";

                if (item.opsiyon2_fiyat != null)
                {
                    fiyat = item.opsiyon2_fiyat.ToString();
                    fiyat = fiyat.Substring(0, fiyat.Length - 2) + " TL";
                }
                listItems.Add(new SelectListItem
                     {
                         Text = item.opsiyon2 + " " + fiyat,
                         Value = item.opsiyon2 + "%" + item.opsiyon2_fiyat.ToString()
                     });
            }
            if (item.opsiyon3 != null)
            {
                string fiyat = "";

                if (item.opsiyon3_fiyat != null)
                {
                    fiyat = item.opsiyon3_fiyat.ToString();
                    fiyat = fiyat.Substring(0, fiyat.Length - 2) + " TL";
                }
                listItems.Add(new SelectListItem
                {
                    Text = item.opsiyon3 + " " + fiyat,
                    Value = item.opsiyon3 + "%" + item.opsiyon3_fiyat.ToString()
                });
            }
        }
    @Html.DropDownList("secilen_opsiyon", listItems, new { @class = "form-control" })
    }
}

