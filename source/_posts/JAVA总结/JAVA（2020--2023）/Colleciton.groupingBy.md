---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# Colleciton.groupingBy

```title: JAVA8 ```

```tag:JAVA```



[java8中的Collectors.groupingBy用法](https://blog.csdn.net/u014231523/article/details/102535902)



```java
 @Data
    class Product{
        Long id;
        Integer num;
        BigDecimal price;
        String name;
        String category;
        public Product(Long id, Integer num, BigDecimal price, String name, String category) {
            this.id = id;
            this.num = num;
            this.price = price;
            this.name = name;
            this.category = category;
        }
    }
    @Test
    public void testGroupingBy(){
        // SpecialwareTypeStatPO specialwareTypeStatPO = new SpecialwareTypeStatPO();
        System.out.println(SpecialwareTypeStatPO.builder().activityId(0).build().getActivityId());



        Product prod1 = new Product(1L, 1, new BigDecimal("15.5"), "面包", "零食");
        Product prod2 = new Product(2L, 2, new BigDecimal("20"), "饼干", "零食");
        Product prod3 = new Product(3L, 3, new BigDecimal("30"), "月饼", "零食");
        Product prod4 = new Product(4L, 3, new BigDecimal("10"), "青岛啤酒", "啤酒");
        Product prod5 = new Product(5L, 10, new BigDecimal("15"), "百威啤酒", "啤酒");
        List<Product> prodList = Lists.newArrayList(prod1, prod2, prod3, prod4, prod5);

        /**
         * 1.分组
         */
        Map<String, List<Product>> prodMap= prodList.stream().collect(Collectors.groupingBy(Product::getCategory));
        System.out.println(prodMap);
        //{"啤酒":[{"category":"啤酒","id":4,"name":"青岛啤酒","num":3,"price":10},{"category":"啤酒","id":5,"name":"百威啤酒","num":10,"price":15}],"零食":[{"category":"零食","id":1,"name":"面包","num":1,"price":15.5},{"category":"零食","id":2,"name":"饼干","num":2,"price":20},{"category":"零食","id":3,"name":"月饼","num":3,"price":30}]}
        Map<String, List<Product>> prodMap1 = prodList.stream().collect(Collectors.groupingBy(item -> item.getCategory() + "_" + item.getName()));
        System.out.println(prodMap1);
        //{"零食_月饼":[{"category":"零食","id":3,"name":"月饼","num":3,"price":30}],"零食_面包":[{"category":"零食","id":1,"name":"面包","num":1,"price":15.5}],"啤酒_百威啤酒":[{"category":"啤酒","id":5,"name":"百威啤酒","num":10,"price":15}],"啤酒_青岛啤酒":[{"category":"啤酒","id":4,"name":"青岛啤酒","num":3,"price":10}],"零食_饼干":[{"category":"零食","id":2,"name":"饼干","num":2,"price":20}]}

        Map<String, List<Product>> prodMap2 = prodList.stream().collect(Collectors.groupingBy(item -> {
            if(item.getNum() < 3) {
                return "3";
            }else {
                return "other";
            }
        }));
        System.out.println(prodMap2);
        //{"other":[{"category":"零食","id":3,"name":"月饼","num":3,"price":30},{"category":"啤酒","id":4,"name":"青岛啤酒","num":3,"price":10},{"category":"啤酒","id":5,"name":"百威啤酒","num":10,"price":15}],"3":[{"category":"零食","id":1,"name":"面包","num":1,"price":15.5},{"category":"零食","id":2,"name":"饼干","num":2,"price":20}]}

        /**
         * 2.多级分组
         */
        Map<String, Map<String, List<Product>>> prodMap3 = prodList.stream().collect(Collectors.groupingBy(Product::getCategory, Collectors.groupingBy(item -> {
            if(item.getNum() < 3) {
                return "3";
            }else {
                return "other";
            }
        })));
        System.out.println(prodMap3);
        //{"啤酒":{"other":[{"category":"啤酒","id":4,"name":"青岛啤酒","num":3,"price":10},{"category":"啤酒","id":5,"name":"百威啤酒","num":10,"price":15}]},"零食":{"other":[{"category":"零食","id":3,"name":"月饼","num":3,"price":30}],"3":[{"category":"零食","id":1,"name":"面包","num":1,"price":15.5},{"category":"零食","id":2,"name":"饼干","num":2,"price":20}]}}

        /**
         * 3.求数量 求和应用
         */
        Map<String, Long> prodMap4 = prodList.stream().collect(Collectors.groupingBy(Product::getCategory, Collectors.counting()));
        System.out.println(prodMap4);
        //{"啤酒":2,"零食":3}
        Map<String, Integer> prodMap5 = prodList.stream().collect(Collectors.groupingBy(Product::getCategory, Collectors.summingInt(Product::getNum)));
        System.out.println(prodMap5);
        //{"啤酒":13,"零食":6}
        /**
         *  **转换为其他接收器
         */
        Map<String, Product> prodMap6 = prodList.stream().collect(Collectors.groupingBy(Product::getCategory, Collectors.collectingAndThen(Collectors.maxBy(Comparator.comparingInt(Product::getNum)), Optional::get)));
        System.out.println(prodMap6);
        //{"啤酒":{"category":"啤酒","id":5,"name":"百威啤酒","num":10,"price":15},"零食":{"category":"零食","id":3,"name":"月饼","num":3,"price":30}}

        Map<String, Map<Long, Product>> collect = prodList.stream().collect(Collectors.groupingBy(Product::getCategory,
                Collectors.collectingAndThen(Collectors.toMap(Product::getId, Function.identity()),//Collecitons.toList(),
                        //list1 -> list1.stream().collect(Collectors.toMap(Product::getId, Product::getName
                        Function.identity())));
        System.out.println(collect);
        Map<String, Set<String>> prodMap7 = prodList.stream().collect(Collectors.groupingBy(Product::getCategory, Collectors.mapping(Product::getName, Collectors.toSet())));
        System.out.println(prodMap7);
    //{"啤酒":["青岛啤酒","百威啤酒"],"零食":["面包","饼干","月饼"]}
    }
```

