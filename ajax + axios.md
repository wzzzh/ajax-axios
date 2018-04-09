# ajax 和 axios 的二次封装 #
**ajax**    

	var _requestLevelTwo = function(url, param, fun) {
	  $.ajax({
	    type: "post",
	    url: apiUrl + url,
	    data: param,
	    dataType: "json",
	    success: function(data) {
	      if(data.code == 201){
	        // delCookie('zhusername');
	        // delCookie('phone');
	        // delCookie('uid');
	        // window.location.href = '/login';
	      }
	      if (data != null) {
	        var _state = data.code;
	        //请求正常
	        if (_state == '200') {
	          fun(data, 200);
	        }
	        //请求异常
	        else {
	          var _error = data.message;
	          fun(data, parseInt(data.message));
	        }
	      }
	    },
	    error: function() {
	      url = url + ":";
	      if (url.indexOf(":51:") > -1 || url.indexOf(":52:") > -1 || url.indexOf(":53:") > -1 || url.indexOf(":54:") > -1 || url.indexOf(":55:") > -1) {
	        return;
	      }
	    }
	  });
	};


**axios**  

	axios.interceptors.request.use(config => {
	  // loading
	  return config
	}, error => {
	  return Promise.reject(error)
	})
	
	axios.interceptors.response.use(response => {
	  return response
	}, error => {
	  return Promise.resolve(error.response)
	})
	
	function checkStatus (response,callback) {
	  console.log(callback);
	  // loading
	  // 如果http状态码正常，则直接返回数据
	  if (response && (response.status === 200 || response.status === 304 || response.status === 400)) {
	    callback(response);
	    // 如果不需要除了data之外的数据，可以直接 return response.data
	  }
	  // 异常状态下，把错误信息返回去
	  return {
	    status: -404,
	    msg: '网络异常'
	  }
	}
	
	function checkCode (res) {
	  // 如果code异常(这里已经包括网络错误，服务器错误，后端抛出的错误)，可以弹出一个错误提示，告诉用户
	  if (res.status === -404) {
	    alert(res.msg)
	  }
	  if (res.data && (!res.data.success)) {
	    alert(res.data.error_msg)
	  }
	  return res
	} 

**axios-post**  

	var axiosPost = function post (url, data, callback) {
	  data.token = getCookie('dsp_token') || 21;
	  return axios({
	    method: 'post',
	    baseURL: 'https://cnodejs.org/api/v1',
	    url:root+url,
	    data: qs.stringify(data),
	    timeout: 10000,
	    headers: {
	      'X-Requested-With': 'XMLHttpRequest',
	      'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
	    }
	  }).then(
	    (response) => {
	      callback(response.data)
	      if(response.data.code == 201){
	        console.log('用户失效')
	      }
	
	    }
	    // (response) => {
	    //   return checkStatus(response)
	    // }
	  )
	  //   .then(
	  //   (res) => {
	  //     return checkCode(res)
	  //   }
	  // )
	}

**axios-get**  

	var axiosGet= function get (url, params,callback) {
	  params.token='20';
	  return axios({
	    method: 'get',
	    baseURL: 'https://cnodejs.org/api/v1',
	    url:root+url,
	    params, // get 请求时带的参数
	    timeout: 10000,
	    headers: {
	      'X-Requested-With': 'XMLHttpRequest'
	    }
	  }).then(
	    (response) => {
	      callback(response.data)
	
	    }
	  )
	}