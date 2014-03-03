public class MetaDataController : Controller
    {
        #region Public Methods and Operators
        public JsonResult GetShareCounter(string url, string type)
        {
            if (type.Equals("googlePlus"))
                return GetPlusOnes(url);
            else if (type.Equals("stumbleupon"))
                return GetStumbleUpons(url);
            else if (type.Equals("facebook"))
                return GetFacebookShares(url);
            return Json(new { count = 0 }, JsonRequestBehavior.AllowGet);
        }

        public JsonResult GetFacebookShares(string url)
        {
            var apiUrl = String.Format("http://api.ak.facebook.com/restserver.php?v=1.0&method=links.getStats&urls={0}&format=json", url);

            var json = GetJsonResult(apiUrl, null);
            if (json == null)
                return null;
            int count = Int32.Parse(json[0]["total_count"].ToString().Replace(".0", ""));

            return Json(new { count = count }, JsonRequestBehavior.AllowGet);
        }

        public JsonResult GetStumbleUpons(string url)
        {
            var apiUrl = "http://www.stumbleupon.com/services/1.01/badge.getinfo?url=" + url + "&callback=?";
            var json = (Dictionary<string, dynamic>)GetJsonResult(apiUrl, null);
            if (json == null)
                return null;
            if (json.Keys.Contains("result") && ((Dictionary<string, object>)json["result"]).Keys.Contains("views"))
            {
                int count = Int32.Parse((json["result"]["views"] ?? "0").ToString().Replace(".0", ""));
                return Json(new { count = count }, JsonRequestBehavior.AllowGet);
            }
            return Json(new { count = 0 }, JsonRequestBehavior.AllowGet);
        }

        public JsonResult GetPlusOnes(string url)
        {
            var googleApiUrl = "https://clients6.google.com/rpc?key=" + AppSettings.GoogleApiKey;
            var postData = @"[{""method"":""pos.plusones.get"",""id"":""p"",""params"":{""nolog"":true,""id"":""" + url + @""",""source"":""widget"",""userId"":""@viewer"",""groupId"":""@self""},""jsonrpc"":""2.0"",""key"":""p"",""apiVersion"":""v1""}]";
            var json = GetJsonResult(googleApiUrl, postData);
            if (json == null)
                return null;
            int count = Int32.Parse(json[0]["result"]["metadata"]["globalCounts"]["count"].ToString().Replace(".0", ""));

            return Json(new { count = count }, JsonRequestBehavior.AllowGet);
        }
        #endregion

        #region Private methods
        private dynamic GetJsonResult(string url, string postData)
        {
            System.Net.HttpWebRequest request = (System.Net.HttpWebRequest)System.Net.WebRequest.Create(url);
            if (!string.IsNullOrEmpty(postData))
            {
                request.Method = "POST";
                request.ContentType = "application/json-rpc";
                request.ContentLength = postData.Length;

                System.IO.Stream writeStream = request.GetRequestStream();
                UTF8Encoding encoding = new UTF8Encoding();
                byte[] bytes = encoding.GetBytes(postData);
                writeStream.Write(bytes, 0, bytes.Length);
                writeStream.Close();
            }
            System.Net.HttpWebResponse response = (System.Net.HttpWebResponse)request.GetResponse();
            System.IO.Stream responseStream = response.GetResponseStream();
            if (responseStream != null)
            {
                System.IO.StreamReader readStream = new System.IO.StreamReader(responseStream, Encoding.UTF8);
                var jsonString = readStream.ReadToEnd();

                readStream.Close();
                responseStream.Close();
                response.Close();

                var json = new System.Web.Script.Serialization.JavaScriptSerializer().Deserialize<dynamic>(jsonString);
                return json;

            }
            return null;
        }
        #endregion
    }
