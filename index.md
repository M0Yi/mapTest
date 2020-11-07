后端主要代码

```
        // 当前坐标系
                $longitude =   $this->request->post('longitude',0);
                $latitude =  $this->request->post('latitude',0);
        
                //查询距离 米
                $distance = 9999999999 ;
        
                // 查询页
                $page = 0;
                $limit = 50;
        
                // 获取表头
                $prefix = Config::get('database.prefix');
                // 查询坐标系附近坐标增加一个distance字段 只要id
                $data  = Db::query('select t.id,t.longitude,t.latitude,
          TRUNCATE(st_distance(point ('.$longitude.', '.$latitude.'),point(t.longitude,t.latitude)),10)* 6370.996 * 1000 as distance
        	from '.$prefix.'moyicosmic_cosmos t
        	HAVING distance >= 0 and distance < '.$distance.'
        	ORDER BY distance
        	LIMIT '.$page*$limit.','. $limit .'
        	');
                if(!$data){
                    $this->error('当前坐标没有数据');
                }
                $arr = [];
                foreach ($data as $index => $item) {
                    $arr[] = $item['id'];
                }
                $cosmos = new Cosmos();
                $list = $cosmos->with(['user']) ->select($arr);
        
                foreach ($list as $index => $item) {
                    $item['distance'] = ceil($data[$index]['distance'] );
                }
                $this->success('',['list'=>$list]);
```